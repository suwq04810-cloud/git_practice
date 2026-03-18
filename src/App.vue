<template>
  <div class="page">
    <HeroHeader />
    <main class="board">
      <TodoInput @add-todo="handleAddTask" />
      <TodoList
        v-model:todos="tasks"
        :filter-status="statusFilter"
        @delete-todo="handleDeleteTask"
        @toggle-status="handleToggleStatus"
        @update-title="handleUpdateTitle"
        @update-filter="handleUpdateFilter"
      />
    </main>
  </div>
</template>

<script setup>
import { onMounted, ref, watch } from 'vue'
import HeroHeader from './components/HeroHeader.vue'
import TodoInput from './components/TodoInput.vue'
import TodoList from './components/TodoList.vue'

const tasks = ref([
  {
    id: 1,
    title: '审核线框图',
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
    tag: '高优先级',
    tagStyle: 'focus',
  },
  {
    id: 3,
    title: '撰写空状态文案',
    meta: '内容 · 14:00',
    status: 'todo',
    tag: '中优先级',
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
    tag: '低优先级',
    tagStyle: 'neutral',
  },
])

const nextId = ref(6)
const STORAGE_KEY = 'todo-list-items'
const statusFilter = ref('all')
const TAG_STYLE_MAP = {
  高优先级: 'focus',
  中优先级: 'neutral',
  低优先级: 'neutral',
  完成: 'calm',
  受阻: 'alert',
}

onMounted(() => {
  try {
    const saved = localStorage.getItem(STORAGE_KEY)
    if (!saved) return
    const parsed = JSON.parse(saved)
    if (Array.isArray(parsed)) {
      tasks.value = parsed.map((task) => ({
        ...task,
        tagStyle: task.tagStyle || TAG_STYLE_MAP[task.tag] || 'neutral',
        meta: task.meta || buildMeta(task.dueRaw),
      }))
      const maxId = parsed.reduce((max, item) => Math.max(max, Number(item.id) || 0), 0)
      nextId.value = Math.max(nextId.value, maxId + 1)
    }
  } catch (error) {
    console.warn('Failed to load tasks from storage', error)
  }
})

watch(
  tasks,
  (value) => {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(value))
  },
  { deep: true },
)

const handleAddTask = ({ title, dueRaw, tag }) => {
  const trimmedTitle = title.trim()
  if (!trimmedTitle) return
  const meta = buildMeta(dueRaw)
  const safeTag = tag || '中优先级'
  const tagStyle = TAG_STYLE_MAP[safeTag] || 'neutral'

  tasks.value.unshift({
    id: nextId.value++,
    title: trimmedTitle,
    meta,
    dueRaw: dueRaw || '',
    status: 'todo',
    tag: safeTag,
    tagStyle,
  })
}

const handleDeleteTask = (id) => {
  tasks.value = tasks.value.filter((task) => task.id !== id)
}

const handleToggleStatus = (id) => {
  tasks.value = tasks.value.map((task) => {
    if (task.id !== id) return task
    const nextStatus = task.status === 'done' ? 'todo' : 'done'
    return { ...task, status: nextStatus }
  })
}

const handleUpdateTitle = ({ id, title }) => {
  const trimmedTitle = title.trim()
  if (!trimmedTitle) return
  tasks.value = tasks.value.map((task) =>
    task.id === id ? { ...task, title: trimmedTitle } : task,
  )
}

const handleUpdateFilter = (value) => {
  statusFilter.value = value
}

const buildMeta = (dueRaw) => {
  if (!dueRaw) return '待办 · 未设置'
  const [datePart, timePart] = dueRaw.split('T')
  if (!datePart) return '待办 · 未设置'
  const [, month, day] = datePart.split('-')
  const timeText = timePart ? ` ${timePart}` : ''
  return `待办 · ${Number(month)} 月 ${Number(day)} 日${timeText}`
}
</script>
