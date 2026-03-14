<template>
  <section class="panel tasks">
    <div class="panel-header">
      <h2 class="panel-title">今日任务</h2>
      <span class="panel-note">按优先级安排节奏</span>
    </div>

    <div v-if="todos.length" class="task-scroll">
      <Draggable
        v-model="list"
        item-key="id"
        class="task-list"
        tag="ul"
        handle=".drag-handle"
        ghost-class="drag-ghost"
        chosen-class="drag-chosen"
        drag-class="dragging"
      >
        <template #item="{ element }">
          <TodoItem
            :todo="element"
            @delete-todo="handleDelete"
            @toggle-status="handleToggleStatus"
            @update-title="handleUpdateTitle"
          />
        </template>
      </Draggable>
    </div>

    <div v-else class="empty-state">
      <div class="empty-illustration" aria-hidden="true">
        <svg viewBox="0 0 120 90" role="presentation">
          <rect x="10" y="18" width="100" height="64" rx="12" fill="#E9EDF5" />
          <rect x="22" y="32" width="76" height="8" rx="4" fill="#C8D2E3" />
          <rect x="22" y="48" width="56" height="8" rx="4" fill="#D6DDEA" />
          <circle cx="30" cy="66" r="6" fill="#C8D2E3" />
          <rect x="42" y="62" width="38" height="8" rx="4" fill="#D6DDEA" />
        </svg>
      </div>
      <p class="empty-title">暂时没有任务</p>
      <p class="empty-subtitle">添加第一条任务开始吧。</p>
    </div>

    <TodoFooter :done-count="doneCount" :total-count="totalCount" />
  </section>
</template>

<script setup>
import { computed } from 'vue'
import Draggable from 'vuedraggable'
import TodoFooter from './TodoFooter.vue'
import TodoItem from './TodoItem.vue'

const emit = defineEmits(['delete-todo', 'toggle-status', 'update-title', 'update:todos'])
const props = defineProps({
  todos: {
    type: Array,
    default: () => [],
  },
})

const list = computed({
  get: () => props.todos,
  set: (value) => emit('update:todos', value),
})

const handleDelete = (id) => {
  emit('delete-todo', id)
}

const handleToggleStatus = (id) => {
  emit('toggle-status', id)
}

const handleUpdateTitle = (payload) => {
  emit('update-title', payload)
}

const doneCount = computed(
  () => props.todos.filter((todo) => todo.status === 'done').length,
)
const totalCount = computed(() => props.todos.length)
</script>
