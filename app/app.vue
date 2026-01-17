<template>
  <div class="app">
    <NuxtRouteAnnouncer />
    <NewMemory />
  </div>
</template>

<script setup>
import NewMemory from './components/NewMemory.vue'
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
