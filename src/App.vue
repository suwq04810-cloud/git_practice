<template>
  <div class="page">
    <HeroHeader />
    <main class="board">
      <TaskInputCard />
      <TaskListCard />
      <NotesCard />
    </main>
    <PageFooter />
  </div>
</template>

<script setup>
import HeroHeader from './components/HeroHeader.vue'
import TaskInputCard from './components/TaskInputCard.vue'
import TaskListCard from './components/TaskListCard.vue'
import NotesCard from './components/NotesCard.vue'
import PageFooter from './components/PageFooter.vue'
</script>
