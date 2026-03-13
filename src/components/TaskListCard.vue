<template>
  <section class="panel tasks">
    <div class="panel-header">
      <h2 class="panel-title">今日任务</h2>
      <div class="progress">
        <div class="progress-bar" :style="progressStyle"></div>
        <span class="progress-text">已完成 {{ doneCount }} / {{ totalCount }}</span>
      </div>
    </div>

    <ul v-if="tasks.length" class="task-list">
      <li
        v-for="task in tasks"
        :key="task.id"
        class="task-item"
        :class="task.status"
      >
        <div class="task-left">
          <span class="check" :class="{ empty: task.status !== 'done' }"></span>
          <div>
            <p class="task-title" :title="task.title">{{ task.title }}</p>
            <p class="task-meta" :title="task.meta">{{ task.meta }}</p>
          </div>
        </div>
        <span class="tag" :class="task.tagStyle">{{ task.tag }}</span>
      </li>
    </ul>

    <div v-else class="empty-state">
      <p class="empty-title">暂时没有任务</p>
      <p class="empty-subtitle">添加第一条任务开始吧。</p>
    </div>
  </section>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
  tasks: {
    type: Array,
    default: () => [],
  },
})

const doneCount = computed(
  () => props.tasks.filter((task) => task.status === 'done').length,
)
const totalCount = computed(() => props.tasks.length)
const progressStyle = computed(() => {
  const percent = totalCount.value === 0 ? 0 : (doneCount.value / totalCount.value) * 100
  const clamped = Math.min(100, Math.max(0, percent))
  return {
    background: `linear-gradient(90deg, #1f1b16 0 ${clamped}%, #f1e6da ${clamped}% 100%)`,
  }
})
</script>
