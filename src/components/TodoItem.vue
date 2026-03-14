<template>
  <li class="task-item" :class="todo.status">
    <div class="task-left">
      <button
        class="check"
        :class="{ checked: todo.status === 'done' }"
        type="button"
        aria-label="切换任务状态"
        @click="toggleStatus"
      ></button>
      <div>
        <p
          v-if="!isEditing"
          class="task-title"
          :title="todo.title"
          @dblclick="startEditing"
        >
          {{ todo.title }}
        </p>
        <input
          v-else
          ref="editInput"
          v-model="draftTitle"
          class="edit-input"
          type="text"
          @keydown.enter="saveEdit"
          @keydown.esc="cancelEdit"
          @blur="saveEdit"
        />
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
import { nextTick, ref } from 'vue'

const emit = defineEmits(['delete-todo', 'toggle-status', 'update-title'])
const props = defineProps({
  todo: {
    type: Object,
    required: true,
  },
})

const pendingDelete = ref(false)
const isEditing = ref(false)
const draftTitle = ref(props.todo.title)
const editInput = ref(null)

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

const toggleStatus = () => {
  emit('toggle-status', props.todo.id)
}

const startEditing = async () => {
  isEditing.value = true
  draftTitle.value = props.todo.title
  await nextTick()
  editInput.value?.focus()
  editInput.value?.select()
}

const saveEdit = () => {
  if (!isEditing.value) return
  const trimmed = draftTitle.value.trim()
  if (trimmed) {
    emit('update-title', { id: props.todo.id, title: trimmed })
  } else {
    draftTitle.value = props.todo.title
  }
  isEditing.value = false
}

const cancelEdit = () => {
  draftTitle.value = props.todo.title
  isEditing.value = false
}
</script>
