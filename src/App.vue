<template>
  <header>
  </header>
  <main>
    <component :is="currentView" />
  </main>
</template>

<script setup lang="ts">
import { routes } from './router'
import { ref, computed, onUnmounted } from 'vue'
const currentPath = ref<string>(window.location.hash)

function updateCurrentPath() {
  currentPath.value = window.location.hash
}
window.addEventListener('hashchange', updateCurrentPath)

const currentView = computed(() => {
  return routes[currentPath.value.slice(1) || '/']
})

onUnmounted(() => {
  window.removeEventListener('hashchange', updateCurrentPath)
})
</script>

<style scoped>

</style>
