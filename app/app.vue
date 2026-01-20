<template>
  <div class="app">
    <NuxtRouteAnnouncer />
    <Timeline v-if="currentView === 'timeline'" @navigate="handleNavigate" />
    <NewMemory v-else @navigate="handleNavigate" />
  </div>
</template>

<script setup>
import { ref } from 'vue'
import NewMemory from './components/NewMemory.vue'
import Timeline from './components/Timeline.vue'

const currentView = ref('timeline')

function handleNavigate(view) {
  if (view === 'new') {
    currentView.value = 'new-memory'
  } else if (view === 'timeline') {
    currentView.value = 'timeline'
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800&display=swap');

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, sans-serif;
  background: #f5f3ef;
  min-height: 100vh;
  color: #1a1a1a;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
</style>
