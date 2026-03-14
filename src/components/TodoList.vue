<template>
  <section class="panel tasks">
    <div class="panel-header">
      <h2 class="panel-title">今日任务</h2>
      <span class="panel-note">按优先级安排节奏</span>
    </div>

    <ul v-if="todos.length" class="task-list">
      <TodoItem
        v-for="todo in todos"
        :key="todo.id"
        :todo="todo"
        @delete-todo="handleDelete"
      />
    </ul>

    <div v-else class="empty-state">
      <p class="empty-title">暂时没有任务</p>
      <p class="empty-subtitle">添加第一条任务开始吧。</p>
    </div>

    <TodoFooter :done-count="doneCount" :total-count="totalCount" />
  </section>
</template>

<script setup>
import { computed } from 'vue'
import TodoFooter from './TodoFooter.vue'
import TodoItem from './TodoItem.vue'

const emit = defineEmits(['delete-todo'])
const props = defineProps({
  todos: {
    type: Array,
    default: () => [],
  },
})

const handleDelete = (id) => {
  emit('delete-todo', id)
}

const doneCount = computed(
  () => props.todos.filter((todo) => todo.status === 'done').length,
)
const totalCount = computed(() => props.todos.length)
</script>
