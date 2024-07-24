<script setup lang="ts">
import BootstrapCollapse from './components/BootstrapCollapse.vue';
import BootstrapCollapseWithoutAnimation from './components/BootstrapCollapseWithoutAnimation.vue';
</script>

<template>
  <div class="d-flex flex-column justify-content-center m-5">
    <div class="mx-5 mb-5">
      <BootstrapCollapse id="withAnimation">
        <template #toggle>Animated toggle!</template>
        <template #content
          ><div class="card card-body">Animated content!</div></template
        >
      </BootstrapCollapse>
    </div>
    <div class="mx-5 mb-5">
      <BootstrapCollapseWithoutAnimation id="withoutAnimation">
        <template #toggle>Unanimated toggle!</template>
        <template #content
          ><div class="card card-body">Unanimated content!</div></template
        >
      </BootstrapCollapseWithoutAnimation>
    </div>
  </div>
</template>

<style lang="scss">
@import 'bootstrap/scss/bootstrap';
</style>
