<script setup lang="ts">
import DraggableList from '../components/DraggableList.vue';
import { tempSlidesData } from '../api';
</script>

<template>
  <div class="wrapper">
    <h1>Slides</h1>
    <section class="slides">
      <DraggableList :items="tempSlidesData" :item-width="'200px'" :item-height="'130px'" />
    </section>
  </div>
</template>

<style scoped>
.wrapper {
  padding: 0 2rem;
  flex-grow: 0;
  align-self: flex-start;
}

.slides {
  height: 80vh;
  overflow-y: auto;
}
</style>
