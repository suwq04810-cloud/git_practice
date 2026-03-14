<template>
  <div class="todo-footer">
    <div class="progress">
      <div class="progress-bar" :style="progressStyle"></div>
      <span class="progress-text">已完成 {{ doneCount }} / {{ totalCount }}</span>
    </div>
    <span class="todo-summary">共 {{ totalCount }} 项</span>
  </div>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
  doneCount: {
    type: Number,
    default: 0,
  },
  totalCount: {
    type: Number,
    default: 0,
  },
})

const progressStyle = computed(() => {
  const percent = props.totalCount === 0 ? 0 : (props.doneCount / props.totalCount) * 100
  const clamped = Math.min(100, Math.max(0, percent))
  return {
    background: `linear-gradient(90deg, var(--color-primary) 0 ${clamped}%, var(--color-border) ${clamped}% 100%)`,
  }
})
</script>
