<script setup lang="ts">
import { computed, ref } from 'vue';
import { Bezier } from 'bezier-js';

// Mimic CSS easing transition
const bezier = new Bezier([
  { x: 0, y: 0 },
  { x: 0.25, y: 0.1 },
  { x: 0.25, y: 1 },
  { x: 1, y: 1 },
]);

interface Props {
  id: string;
  duration?: number;
}

const props = withDefaults(defineProps<Props>(), {
  duration: 350,
});

const show = ref(false);
const height = ref('');

const zero = ref(0);

const collapseStyles = computed(() => ({
  height: height.value,
  overflow: height.value ? 'hidden' : undefined,
}));

function onBeforeEnter() {
  height.value = '0px';
}

function onEnter(el: Element, done: () => void) {
  zero.value = performance.now();
  requestAnimationFrame(() => animate(zero.value, el, (t) => ease(t), done));
}

function onBeforeLeave(el: Element) {
  height.value = `${el.scrollHeight}px`;
}

function onLeave(el: Element, done: () => void) {
  zero.value = performance.now();
  requestAnimationFrame(() =>
    animate(zero.value, el, (t) => ease(1 - t), done)
  );
}

function animate(
  timestamp: number,
  el: Element,
  easingFn: (t: number) => number,
  done: () => void
) {
  const value = (timestamp - zero.value) / props.duration;
  if (value < 1) {
    height.value = `${easingFn(value) * el.scrollHeight}px`;
    requestAnimationFrame((t) => animate(t, el, easingFn, done));
  } else {
    height.value = '';
    done();
  }
}

function ease(t: number): number {
  return bezier.get(t).y;
}
</script>

<template>
  <button
    type="button"
    class="btn btn-primary"
    :aria-controls="id"
    :aria-expanded="show"
    @click="show = !show"
  >
    <slot name="toggle" />
  </button>
  <Transition
    :css="false"
    @before-enter="onBeforeEnter"
    @enter="onEnter"
    @before-leave="onBeforeLeave"
    @leave="onLeave"
  >
    <div v-show="show" :id :style="collapseStyles">
      <slot name="content" />
    </div>
  </Transition>
</template>
