<script setup lang="ts">
import { ref } from 'vue';

interface Props {
  id: String;
}

defineProps<Props>();

const show = ref(false);
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
  <div v-show="show" :id>
    <slot name="content" />
  </div>
</template>
