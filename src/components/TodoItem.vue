<template>
  <li class="task-item" :class="todo.status">
    <div class="task-left">
      <span class="check" :class="{ empty: todo.status !== 'done' }"></span>
      <div>
        <p class="task-title" :title="todo.title">{{ todo.title }}</p>
        <p class="task-meta" :title="todo.meta">{{ todo.meta }}</p>
      </div>
    </div>
    <div class="task-actions">
      <span class="tag" :class="todo.tagStyle">{{ todo.tag }}</span>
      <div class="delete-wrap">
        <button class="icon-btn" type="button" @click="toggleConfirm">
          删除
        </button>
        <div v-if="pendingDelete" class="confirm-pop">
          <span class="confirm-text">确认删除该任务？</span>
          <div class="confirm-actions">
            <button class="confirm-btn danger" type="button" @click="confirmDelete">
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
</template>

<script setup>
import { ref } from 'vue'

const emit = defineEmits(['delete-todo'])
const props = defineProps({
  todo: {
    type: Object,
    required: true,
  },
})

const pendingDelete = ref(false)

const toggleConfirm = () => {
  pendingDelete.value = !pendingDelete.value
}

const confirmDelete = () => {
  emit('delete-todo', props.todo.id)
  pendingDelete.value = false
}

const cancelDelete = () => {
  pendingDelete.value = false
}
</script>
