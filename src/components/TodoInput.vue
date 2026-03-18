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
const tagOptions = ['高优先级', '中优先级', '低优先级']
const isTagMenuOpen = ref(false)
const tagSelectRef = ref(null)
const TAG_TONE_CLASS_MAP = {
  高优先级: 'focus-tone',
  中优先级: 'medium-tone',
  低优先级: 'low-tone',
}

const getTagToneClass = (value) => TAG_TONE_CLASS_MAP[value] || ''
const tagToneClass = computed(() => getTagToneClass(tag.value))

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

onMounted(() => {
  document.addEventListener('mousedown', closeTagMenuOnOutside)
})

onBeforeUnmount(() => {
  document.removeEventListener('mousedown', closeTagMenuOnOutside)
})

const submitTodo = () => {
  emit('add-todo', { title: title.value, dueRaw: due.value, tag: tag.value })
  title.value = ''
  due.value = ''
}
</script>
