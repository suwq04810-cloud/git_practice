<template>
  <section class="panel tasks">
    <div class="panel-header">
      <div>
        <h2 class="panel-title">今日任务</h2>
        <span class="panel-note">按状态筛选 · 拖拽排序</span>
      </div>
      <div class="filter-tabs">
        <button
          v-for="item in filters"
          :key="item.value"
          class="filter-btn"
          :class="{ active: filterStatus === item.value }"
          type="button"
          @click="updateFilter(item.value)"
        >
          {{ item.label }}
        </button>
      </div>
    </div>

    <div v-if="visibleTodos.length" class="task-scroll">
      <section
        v-for="group in groupedTodos"
        :key="group.id"
        class="task-group"
      >
        <h3 class="task-group-title">{{ group.title }}</h3>
        <Draggable
          :list="group.items"
          item-key="id"
          class="task-list"
          tag="ul"
          handle=".drag-handle"
          ghost-class="drag-ghost"
          chosen-class="drag-chosen"
          drag-class="dragging"
          @update:list="(value) => updateGroupOrder(group.id, value)"
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
      </section>
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

const emit = defineEmits([
  'delete-todo',
  'toggle-status',
  'update-title',
  'update:todos',
  'update-filter',
])
const props = defineProps({
  todos: {
    type: Array,
    default: () => [],
  },
  filterStatus: {
    type: String,
    default: 'all',
  },
})

const filters = [
  { value: 'all', label: '全部' },
  { value: 'todo', label: '进行中' },
  { value: 'done', label: '已完成' },
  { value: 'blocked', label: '受阻' },
]

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

const updateFilter = (value) => {
  emit('update-filter', value)
}

const visibleTodos = computed(() => {
  if (props.filterStatus === 'all') return props.todos
  return props.todos.filter((todo) => todo.status === props.filterStatus)
})

const groupOrder = [
  { id: 'overdue', title: '已逾期' },
  { id: 'today', title: '今天' },
  { id: 'upcoming', title: '未来 7 天' },
  { id: 'later', title: '更晚' },
  { id: 'none', title: '未设置截止' },
]

const groupedTodos = computed(() => {
  const buckets = {
    overdue: [],
    today: [],
    upcoming: [],
    later: [],
    none: [],
  }
  visibleTodos.value.forEach((todo) => {
    const bucket = getBucketId(todo)
    buckets[bucket].push(todo)
  })
  return groupOrder
    .map((group) => ({ ...group, items: buckets[group.id] }))
    .filter((group) => group.items.length > 0)
})

const updateGroupOrder = (groupId, newItems) => {
  const groups = groupedTodos.value
  const byId = new Map(groups.map((group) => [group.id, group.items]))
  byId.set(groupId, newItems)

  const newVisible = []
  groups.forEach((group) => {
    const items = byId.get(group.id) || []
    newVisible.push(...items)
  })

  const visibleIds = new Set(newVisible.map((todo) => todo.id))
  const reordered = []
  let index = 0
  props.todos.forEach((todo) => {
    if (visibleIds.has(todo.id)) {
      reordered.push(newVisible[index])
      index += 1
    } else {
      reordered.push(todo)
    }
  })
  emit('update:todos', reordered)
}

const getBucketId = (todo) => {
  if (!todo.dueRaw) return 'none'
  const due = new Date(todo.dueRaw)
  if (Number.isNaN(due.getTime())) return 'none'
  const now = new Date()
  const startToday = new Date(now.getFullYear(), now.getMonth(), now.getDate())
  const startTomorrow = new Date(now.getFullYear(), now.getMonth(), now.getDate() + 1)
  const startNextWeek = new Date(now.getFullYear(), now.getMonth(), now.getDate() + 7)
  if (due < startToday) return 'overdue'
  if (due < startTomorrow) return 'today'
  if (due < startNextWeek) return 'upcoming'
  return 'later'
}
</script>
