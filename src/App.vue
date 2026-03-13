<template>
  <div class="page">
    <HeroHeader />
    <main class="board">
      <TaskInputCard @add-task="handleAddTask" />
      <TaskListCard :tasks="tasks" @delete-task="handleDeleteTask" />
      <NotesCard />
    </main>
    <PageFooter />
  </div>
</template>

<script setup>
import { ref } from 'vue'
import HeroHeader from './components/HeroHeader.vue'
import TaskInputCard from './components/TaskInputCard.vue'
import TaskListCard from './components/TaskListCard.vue'
import NotesCard from './components/NotesCard.vue'
import PageFooter from './components/PageFooter.vue'

const tasks = ref([
  {
    id: 1,
    title: '评审线框图',
    meta: '设计 · 09:30',
    status: 'done',
    tag: '完成',
    tagStyle: 'calm',
  },
  {
    id: 2,
    title: '制作主视觉动效原型',
    meta: '动效 · 11:00',
    status: 'todo',
    tag: '高优先',
    tagStyle: 'focus',
  },
  {
    id: 3,
    title: '撰写空状态文案',
    meta: '内容 · 14:00',
    status: 'todo',
    tag: '中优先',
    tagStyle: 'neutral',
  },
  {
    id: 4,
    title: '与后端团队对齐',
    meta: '协作 · 等待中',
    status: 'blocked',
    tag: '受阻',
    tagStyle: 'alert',
  },
  {
    id: 5,
    title: '打磨设置图标',
    meta: '界面 · 16:30',
    status: 'todo',
    tag: '低优先',
    tagStyle: 'neutral',
  },
])

const nextId = ref(6)

const handleAddTask = ({ title, due }) => {
  const trimmedTitle = title.trim()
  if (!trimmedTitle) return
  const meta = due ? `待办 · ${due}` : '待办 · 未设置'

  tasks.value.unshift({
    id: nextId.value++,
    title: trimmedTitle,
    meta,
    status: 'todo',
    tag: '新任务',
    tagStyle: 'neutral',
  })
}

const handleDeleteTask = (id) => {
  tasks.value = tasks.value.filter((task) => task.id !== id)
}
</script>
