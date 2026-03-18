<template>
  <section class="panel">
    <div class="panel-header">
      <h2 class="panel-title">添加任务</h2>
      <span class="panel-note">快速记录今天的重点</span>
    </div>

    <form ref="todoFormRef" class="task-input" @submit.prevent="submitTodo">
      <div class="input-field">
        <label class="input-label" for="todo-title">任务名称</label>
        <input
          id="todo-title"
          ref="titleInputRef"
          v-model="title"
          class="input-box"
          type="text"
          placeholder="例如：设计新手引导流程"
          @keydown.enter.exact.prevent="submitTodoByEnter"
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
          <span class="input-date-display" :class="{ placeholder: !due }">
            {{ due ? formatDueDisplay(due) : '请选择截止时间' }}
          </span>
        </div>
      </div>
      <div class="input-field">
        <label class="input-label" for="todo-tag">标签</label>
        <div ref="tagSelectRef" class="tag-select">
          <button
            id="todo-tag"
            class="input-box tag-trigger"
            :class="[tagToneClass, { 'is-placeholder': !tag }]"
            type="button"
            @click="toggleTagMenu"
          >
            <span class="tag-trigger-main">
              <span v-if="tag" class="tag-trigger-dot" :class="tagToneClass"></span>
              <span class="tag-trigger-text">{{ tag || '请选择标签' }}</span>
            </span>
            <span class="tag-trigger-arrow" :class="{ open: isTagMenuOpen }"></span>
          </button>
          <ul v-if="isTagMenuOpen" class="tag-menu">
            <li v-for="option in tagOptions" :key="option">
              <button
                type="button"
                class="tag-option"
                :class="[getTagToneClass(option), { active: option === tag }]"
                @click="selectTag(option)"
              >
                {{ option }}
              </button>
            </li>
          </ul>
        </div>
      </div>
      <button class="primary-btn" type="submit">添加任务</button>
    </form>
    <div class="shortcut-hint" role="status" aria-live="polite">
      <p class="shortcut-guide">快捷键：/ 聚焦任务名称，1/2/3 设置优先级，Enter 直接新增</p>
      <p class="shortcut-state" :class="`tone-${shortcutTone}`">
        当前优先级：<span class="shortcut-priority">{{ effectiveTag }}</span> · {{ shortcutStatus }}
      </p>
    </div>

    <div class="filters">
      <span class="filter active">全部</span>
      <span class="filter">进行中</span>
      <span class="filter">已完成</span>
      <span class="filter">受阻</span>
    </div>
  </section>
</template>

<script setup>
import { computed, onBeforeUnmount, onMounted, ref } from 'vue'

const emit = defineEmits(['add-todo'])
const title = ref('')
const due = ref('')
const tag = ref('')
const todoFormRef = ref(null)
const titleInputRef = ref(null)
const tagOptions = ['高优先级', '中优先级', '低优先级']
const isTagMenuOpen = ref(false)
const tagSelectRef = ref(null)
const shortcutStatus = ref('待命中：先按 / 聚焦输入框，再录入任务')
const shortcutTone = ref('neutral')
let shortcutTimer = 0
const TAG_TONE_CLASS_MAP = {
  高优先级: 'focus-tone',
  中优先级: 'medium-tone',
  低优先级: 'low-tone',
}
const PRIORITY_KEY_MAP = {
  '1': '高优先级',
  '2': '中优先级',
  '3': '低优先级',
}

const getTagToneClass = (value) => TAG_TONE_CLASS_MAP[value] || ''
const tagToneClass = computed(() => getTagToneClass(tag.value))
const effectiveTag = computed(() => tag.value || '中优先级')

const resetShortcutStatus = () => {
  shortcutStatus.value = '待命中：先按 / 聚焦输入框，再录入任务'
  shortcutTone.value = 'neutral'
}

const setShortcutStatus = (message, tone = 'neutral') => {
  shortcutStatus.value = message
  shortcutTone.value = tone
  window.clearTimeout(shortcutTimer)
  shortcutTimer = window.setTimeout(() => {
    resetShortcutStatus()
  }, 2200)
}

const isEditableTarget = (target) => {
  if (!target || !(target instanceof HTMLElement)) return false
  if (target.isContentEditable) return true
  return ['input', 'textarea', 'select'].includes(target.tagName.toLowerCase())
}

const isFocusInsideForm = () => todoFormRef.value?.contains(document.activeElement)

const focusTitleInput = () => {
  titleInputRef.value?.focus()
  if (titleInputRef.value) {
    const pos = title.value.length
    titleInputRef.value.setSelectionRange(pos, pos)
  }
}

const toggleTagMenu = () => {
  isTagMenuOpen.value = !isTagMenuOpen.value
}

const selectTag = (value) => {
  tag.value = value
  isTagMenuOpen.value = false
}

const closeTagMenuOnOutside = (event) => {
  if (!tagSelectRef.value?.contains(event.target)) {
    isTagMenuOpen.value = false
  }
}

const handleGlobalShortcut = (event) => {
  if (event.defaultPrevented || event.isComposing) return

  if (event.key === '/') {
    if (isEditableTarget(event.target)) return
    event.preventDefault()
    isTagMenuOpen.value = false
    focusTitleInput()
    setShortcutStatus('已聚焦到任务名称，输入后按 Enter 可直接新增', 'success')
    return
  }

  const mappedTag = PRIORITY_KEY_MAP[event.key]
  if (!mappedTag || event.metaKey || event.ctrlKey || event.altKey) return
  if (!isFocusInsideForm()) return

  const isTargetEditable = isEditableTarget(event.target)
  const isTitleFocused = document.activeElement === titleInputRef.value
  if (isTargetEditable && (!isTitleFocused || title.value.trim())) {
    if (isTitleFocused && title.value.trim()) {
      setShortcutStatus('正在编辑标题，数字会按普通文本输入', 'neutral')
    }
    return
  }

  event.preventDefault()
  selectTag(mappedTag)
  setShortcutStatus(`已切换优先级：${mappedTag}`, 'success')
}

onMounted(() => {
  document.addEventListener('mousedown', closeTagMenuOnOutside)
  document.addEventListener('keydown', handleGlobalShortcut)
})

onBeforeUnmount(() => {
  document.removeEventListener('mousedown', closeTagMenuOnOutside)
  document.removeEventListener('keydown', handleGlobalShortcut)
  window.clearTimeout(shortcutTimer)
})

const submitTodo = () => {
  emit('add-todo', { title: title.value, dueRaw: due.value, tag: tag.value })
  title.value = ''
  due.value = ''
}

const submitTodoByEnter = () => {
  if (!title.value.trim()) {
    setShortcutStatus('任务名称为空，未新增任务', 'warn')
    return
  }
  submitTodo()
  setShortcutStatus('已通过 Enter 新增任务，可继续录入', 'success')
  focusTitleInput()
}

const formatDueDisplay = (value) => {
  const [datePart, timePart] = value.split('T')
  if (!datePart) return '请选择截止时间'
  const [year, month, day] = datePart.split('-')
  const safeYear = Number(year)
  const safeMonth = Number(month)
  const safeDay = Number(day)
  if (!safeYear || !safeMonth || !safeDay) return '请选择截止时间'
  const timeText = timePart ? ` ${timePart.slice(0, 5)}` : ''
  return `${safeYear}-${String(safeMonth).padStart(2, '0')}-${String(safeDay).padStart(2, '0')}${timeText}`
}
</script>
