<template>
  <div class="marquee" ref="marquee">
    <div ref="inner" class="marquee__inner">
      <template v-if="dir === 'ltr'">
        <div
          v-for="item in clones"
          :key="`node-${item}`"
          class="marquee__row test"
        >
          <slot />
        </div>
      </template>
      <div class="marquee__row">
        <slot />
      </div>
      <template v-if="dir === 'rtl'">
        <div
          v-for="item in clones"
          :key="`node-${item}`"
          class="marquee__row"
        >
          <slot />
        </div>
      </template>
    </div>
  </div>
</template>

<script setup lang="ts">
import {
  computed, useSlots, ref, onMounted, nextTick,
} from 'vue';

interface Props {
  speed?: number
  dir?: 'ltr' | 'rtl'
  outerGap?: number
  innerGap?: number
  copy?: boolean
}

const props = withDefaults(defineProps<Props>(), {
  speed: 1000,
  dir: 'ltr',
  innerGap: 10,
  outerGap: 10,
  copy: true,
});

const slots = useSlots();

const marquee = ref<HTMLElement | null>(null);
const inner = ref<HTMLElement | null>(null);
const slotsWidth = computed(() => {
  let w = 0;
  if (!slots || !slots.default || !marquee.value) {
    return w;
  }
  marquee.value.childNodes[0].childNodes.forEach((item) => {
    if (item instanceof HTMLElement) {
      w += item.clientWidth;
    }
  });
  return w;
});
const containerWidth = computed(() => {
  if (!marquee.value) {
    return 0;
  }
  return marquee.value.clientWidth;
});
const outerGap = computed(() => `${props.outerGap}px`);
const innerGap = computed(() => `${props.innerGap}px`);
const clones = ref(0);

function handleMarquee() {
  if (!marquee.value || !inner.value) {
    return;
  }

  const container = inner.value as HTMLElement;
  const content = inner.value.children[0] as HTMLElement;
  const elWidth = content.clientWidth;
  if (props.copy) {
    const count = Math.floor(containerWidth.value / slotsWidth.value) + 1;
    for (let i = 0; i < count; i += 1) {
      clones.value += 1;
    }
  }

  let progress = props.outerGap;
  if (props.dir === 'ltr') {
    progress = -elWidth;
  }
  function loop() {
    if (props.dir === 'rtl') {
      progress -= props.speed / 1000;
      if (progress <= -elWidth) { progress = props.outerGap; }
    } else {
      progress += props.speed / 1000;
      if (progress >= props.outerGap) { progress = -elWidth; }
    }

    container.style.transform = `translateX(${progress}px)`;

    window.requestAnimationFrame(loop);
  }
  loop();
}

onMounted(() => {
  nextTick(() => {
    handleMarquee();
  });
});
</script>

<style lang="scss" scoped>
.marquee__inner {
  display: flex;
  gap: v-bind(outerGap);
}

.marquee__row {
  display: flex;
  gap: v-bind(innerGap);
}
</style>
