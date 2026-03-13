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
            <p class="task-title">{{ task.title }}</p>
            <p class="task-meta">{{ task.meta }}</p>
          </div>
        </div>
        <div class="task-actions">
          <span class="tag" :class="task.tagStyle">{{ task.tag }}</span>
          <div class="delete-wrap">
            <button class="icon-btn" type="button" @click="toggleConfirm(task.id)">
              删除
            </button>
            <div v-if="pendingDeleteId === task.id" class="confirm-pop">
              <span class="confirm-text">确认删除该任务？</span>
              <div class="confirm-actions">
                <button class="confirm-btn danger" type="button" @click="confirmDelete(task.id)">
                  确认
                </button>
                <button class="confirm-btn" type="button" @click="cancelDelete">
                  取消
                </button>
              </div>
            </div>
          </div>
        </div>
      </li>
    </ul>

    <div v-else class="empty-state">
      <p class="empty-title">暂时没有任务</p>
      <p class="empty-subtitle">添加第一条任务开始吧。</p>
    </div>
  </section>
</template>

<script setup>
import { computed, ref } from 'vue'

const emit = defineEmits(['delete-task'])
const props = defineProps({
  tasks: {
    type: Array,
    default: () => [],
  },
})

const pendingDeleteId = ref(null)

const toggleConfirm = (id) => {
  pendingDeleteId.value = pendingDeleteId.value === id ? null : id
}

const confirmDelete = (id) => {
  emit('delete-task', id)
  pendingDeleteId.value = null
}

const cancelDelete = () => {
  pendingDeleteId.value = null
}

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
