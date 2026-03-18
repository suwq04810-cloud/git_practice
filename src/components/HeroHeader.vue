<template>
  <header class="hero">
    <div class="hero-badge">今日</div>

    <h1
      v-if="!isEditingTitle"
      class="hero-title is-editable"
      title="双击编辑标题"
      @dblclick="startTitleEdit"
    >
      {{ boardTitle }}
    </h1>
    <input
      v-else
      ref="titleInputRef"
      v-model="titleDraft"
      class="hero-title-input"
      maxlength="48"
      @blur="saveTitle"
      @keydown="handleTitleKeydown"
    />

    <p
      v-if="!isEditingMotto"
      class="hero-subtitle is-editable"
      title="双击编辑励志文案"
      @dblclick="startMottoEdit"
    >
      {{ boardMotto }}
    </p>
    <input
      v-else
      ref="mottoInputRef"
      v-model="mottoDraft"
      class="hero-subtitle-input"
      maxlength="120"
      @blur="saveMotto"
      @keydown="handleMottoKeydown"
    />

    <p class="hero-edit-status" :class="`is-${statusTone}`">{{ statusText }}</p>

    <div class="hero-meta">
      <span class="meta-chip">个人</span>
      <span class="meta-chip">{{ weekLabel }}</span>
      <span class="meta-chip">{{ timeLabel }}</span>
    </div>
  </header>
</template>

<script setup>
import { computed, nextTick, onBeforeUnmount, onMounted, ref } from 'vue'

const TITLE_KEY = 'kanban-hero-title'
const MOTTO_KEY = 'kanban-hero-motto'
const DEFAULT_TITLE = '专注看板'
const DEFAULT_MOTTO = '轻量、安静的待办布局，帮助我们快速梳理节奏。'
const DEFAULT_STATUS = '双击标题或文案可编辑'
const EDITING_STATUS = '编辑中：Enter 保存，Esc 取消，失焦自动保存'

const now = ref(new Date())
const boardTitle = ref(DEFAULT_TITLE)
const boardMotto = ref(DEFAULT_MOTTO)
const titleDraft = ref('')
const mottoDraft = ref('')
const isEditingTitle = ref(false)
const isEditingMotto = ref(false)
const statusText = ref(DEFAULT_STATUS)
const statusTone = ref('idle')
const titleInputRef = ref(null)
const mottoInputRef = ref(null)

let timerId = null
let statusTimerId = null

const getWeekOfYear = (date) => {
  const target = new Date(Date.UTC(date.getFullYear(), date.getMonth(), date.getDate()))
  const dayNumber = (target.getUTCDay() + 6) % 7
  target.setUTCDate(target.getUTCDate() - dayNumber + 3)

  const firstThursday = new Date(Date.UTC(target.getUTCFullYear(), 0, 4))
  const firstDayNumber = (firstThursday.getUTCDay() + 6) % 7
  firstThursday.setUTCDate(firstThursday.getUTCDate() - firstDayNumber + 3)

  const msInWeek = 7 * 24 * 60 * 60 * 1000
  return 1 + Math.round((target - firstThursday) / msInWeek)
}

const pad = (value) => String(value).padStart(2, '0')

const weekLabel = computed(() => `第 ${getWeekOfYear(now.value)} 周`)

const timeLabel = computed(() => {
  const month = now.value.getMonth() + 1
  const day = now.value.getDate()
  const hours = pad(now.value.getHours())
  const minutes = pad(now.value.getMinutes())
  const seconds = pad(now.value.getSeconds())
  return `${month} 月 ${day} 日 ${hours}:${minutes}:${seconds}`
})

const readStorage = (key, fallbackValue) => {
  try {
    const saved = localStorage.getItem(key)
    if (typeof saved !== 'string') return fallbackValue
    const trimmed = saved.trim()
    return trimmed || fallbackValue
  } catch (error) {
    console.warn(`Failed to load ${key} from storage`, error)
    return fallbackValue
  }
}

const writeStorage = (key, value) => {
  try {
    localStorage.setItem(key, value)
  } catch (error) {
    console.warn(`Failed to save ${key} to storage`, error)
  }
}

const normalizeValue = (value, fallback) => {
  const trimmed = value.trim()
  return trimmed || fallback
}

const clearStatusTimer = () => {
  if (statusTimerId !== null) {
    window.clearTimeout(statusTimerId)
    statusTimerId = null
  }
}

const setStatus = (text, tone = 'idle', autoReset = false) => {
  clearStatusTimer()
  statusText.value = text
  statusTone.value = tone
  if (!autoReset) return

  statusTimerId = window.setTimeout(() => {
    statusText.value = DEFAULT_STATUS
    statusTone.value = 'idle'
    statusTimerId = null
  }, 1500)
}

const startTitleEdit = async () => {
  if (isEditingTitle.value) return
  if (isEditingMotto.value) saveMotto()
  titleDraft.value = boardTitle.value
  isEditingTitle.value = true
  setStatus(EDITING_STATUS, 'editing')
  await nextTick()
  titleInputRef.value?.focus()
  titleInputRef.value?.select()
}

const saveTitle = () => {
  if (!isEditingTitle.value) return
  const nextTitle = normalizeValue(titleDraft.value, boardTitle.value)
  const changed = nextTitle !== boardTitle.value
  boardTitle.value = nextTitle
  isEditingTitle.value = false
  titleDraft.value = boardTitle.value
  if (changed) {
    writeStorage(TITLE_KEY, boardTitle.value)
    setStatus('标题已保存', 'saved', true)
    return
  }
  setStatus(DEFAULT_STATUS, 'idle')
}

const cancelTitle = () => {
  if (!isEditingTitle.value) return
  isEditingTitle.value = false
  titleDraft.value = boardTitle.value
  setStatus('已取消标题修改', 'idle', true)
}

const handleTitleKeydown = (event) => {
  if (event.isComposing) return
  if (event.key === 'Enter') {
    event.preventDefault()
    saveTitle()
    return
  }
  if (event.key === 'Escape') {
    event.preventDefault()
    cancelTitle()
  }
}

const startMottoEdit = async () => {
  if (isEditingMotto.value) return
  if (isEditingTitle.value) saveTitle()
  mottoDraft.value = boardMotto.value
  isEditingMotto.value = true
  setStatus(EDITING_STATUS, 'editing')
  await nextTick()
  mottoInputRef.value?.focus()
  mottoInputRef.value?.select()
}

const saveMotto = () => {
  if (!isEditingMotto.value) return
  const nextMotto = normalizeValue(mottoDraft.value, boardMotto.value)
  const changed = nextMotto !== boardMotto.value
  boardMotto.value = nextMotto
  isEditingMotto.value = false
  mottoDraft.value = boardMotto.value
  if (changed) {
    writeStorage(MOTTO_KEY, boardMotto.value)
    setStatus('励志文案已保存', 'saved', true)
    return
  }
  setStatus(DEFAULT_STATUS, 'idle')
}

const cancelMotto = () => {
  if (!isEditingMotto.value) return
  isEditingMotto.value = false
  mottoDraft.value = boardMotto.value
  setStatus('已取消文案修改', 'idle', true)
}

const handleMottoKeydown = (event) => {
  if (event.isComposing) return
  if (event.key === 'Enter') {
    event.preventDefault()
    saveMotto()
    return
  }
  if (event.key === 'Escape') {
    event.preventDefault()
    cancelMotto()
  }
}

onMounted(() => {
  boardTitle.value = readStorage(TITLE_KEY, DEFAULT_TITLE)
  boardMotto.value = readStorage(MOTTO_KEY, DEFAULT_MOTTO)

  timerId = window.setInterval(() => {
    now.value = new Date()
  }, 1000)
})

onBeforeUnmount(() => {
  if (timerId !== null) {
    window.clearInterval(timerId)
  }
  clearStatusTimer()
})
</script>
