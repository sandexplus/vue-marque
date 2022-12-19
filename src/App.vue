<template>
  <div class="test">
    <vue-marque v-bind="data">
        <p>123123</p>
        <p>321321</p>
        <img src="@/assets/logo.png" alt="">
        <p>dfgjdhngsdfgnskfgnksfdn</p>
        <img src="@/assets/logo.png" alt="">
    </vue-marque>
  </div>
</template>

<script lang="ts" setup>
import VueMarque from '@/components/VueMarque.vue';

const data = {
  copy: false,
  innerGap: 50,
  outerGap: 150,
  speed: 3000,
  dir: 'rtl',
};
</script>

<style lang="scss" scoped>
.test {
  width: 100%;
  border: 1px solid black;
  overflow: hidden;
}

p {
  background: #00000050;
}
.flex  {
  display: flex;
  gap: 10px;
}
</style>
