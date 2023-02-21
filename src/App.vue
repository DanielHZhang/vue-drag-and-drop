<script setup lang="ts">
import { RouterView } from 'vue-router';
</script>

<template>
  <main class="main">
    <RouterView />
  </main>
</template>

<style scoped>
.main {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}
</style>
