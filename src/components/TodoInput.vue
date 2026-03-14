<template>
  <section class="panel">
    <div class="panel-header">
      <h2 class="panel-title">添加任务</h2>
      <span class="panel-note">快速记录今天的重点</span>
    </div>

    <form class="task-input" @submit.prevent="submitTodo">
      <div class="input-field">
        <label class="input-label" for="todo-title">任务名称</label>
        <input
          id="todo-title"
          v-model="title"
          class="input-box"
          type="text"
          placeholder="例如：设计新手引导流程"
        />
      </div>
      <div class="input-field">
        <label class="input-label" for="todo-due">截止</label>
        <div class="input-date">
          <input
            id="todo-due"
            v-model="due"
            class="input-box input-date-box"
            type="datetime-local"
          />
        </div>
      </div>
      <button class="primary-btn" type="submit">添加任务</button>
    </form>

    <div class="filters">
      <span class="filter active">全部</span>
      <span class="filter">进行中</span>
      <span class="filter">已完成</span>
      <span class="filter">受阻</span>
    </div>
  </section>
</template>

<script setup>
import { ref } from 'vue'

const emit = defineEmits(['add-todo'])
const title = ref('')
const due = ref('')

const submitTodo = () => {
  emit('add-todo', { title: title.value, due: formatDate(due.value) })
  title.value = ''
  due.value = ''
}

const formatDate = (value) => {
  if (!value) return ''
  const [datePart, timePart] = value.split('T')
  if (!datePart) return ''
  const [, month, day] = datePart.split('-')
  const timeText = timePart ? ` ${timePart}` : ''
  return `${Number(month)} 月 ${Number(day)} 日${timeText}`
}
</script>
