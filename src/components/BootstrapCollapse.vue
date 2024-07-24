<script setup lang="ts">
import { ref } from 'vue';
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
const animating = ref(false);

const zero = ref(0);

function onBeforeEnter(el: Element) {
  (el as HTMLElement).style.height = '0px';
  animating.value = true;
}

function onEnter(el: Element, done: () => void) {
  zero.value = performance.now();
  requestAnimationFrame(() => animate(zero.value, el, (t) => ease(t), done));
}

function onAfterEnter() {
  animating.value = false;
}

function onBeforeLeave(el: Element) {
  (el as HTMLElement).style.height = `${el.scrollHeight}px`;
  animating.value = true;
}

function onLeave(el: Element, done: () => void) {
  zero.value = performance.now();
  requestAnimationFrame(() =>
    animate(zero.value, el, (t) => ease(1 - t), done)
  );
}

function onAfterLeave() {
  animating.value = false;
}

function animate(
  timestamp: number,
  el: Element,
  easingFn: (t: number) => number,
  done: () => void
) {
  const value = (timestamp - zero.value) / props.duration;
  if (value < 1) {
    (el as HTMLElement).style.height = `${easingFn(value) * el.scrollHeight}px`;
    requestAnimationFrame((t) => animate(t, el, easingFn, done));
  } else {
    (el as HTMLElement).style.height = '';
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
    @after-enter="onAfterEnter"
    @before-leave="onBeforeLeave"
    @leave="onLeave"
    @after-leave="onAfterLeave"
  >
    <div v-show="show" :id :class="{ 'overflow-hidden': animating }">
      <slot name="content" />
    </div>
  </Transition>
</template>
