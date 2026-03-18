<template>
  <header class="hero">
    <div class="hero-badge">今日</div>
    <h1 class="hero-title">专注看板</h1>
    <p class="hero-subtitle">
      轻量、安静的待办布局，帮助我们快速梳理节奏。
    </p>
    <div class="hero-meta">
      <span class="meta-chip">个人</span>
      <span class="meta-chip">{{ weekLabel }}</span>
      <span class="meta-chip">{{ timeLabel }}</span>
    </div>
  </header>
</template>

<script setup>
import { computed, onBeforeUnmount, onMounted, ref } from 'vue'

const now = ref(new Date())
let timerId = null

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

onMounted(() => {
  timerId = window.setInterval(() => {
    now.value = new Date()
  }, 1000)
})

onBeforeUnmount(() => {
  if (timerId !== null) {
    window.clearInterval(timerId)
  }
})
</script>
