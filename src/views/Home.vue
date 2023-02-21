<script setup lang="ts">
import DraggableList from '../components/DraggableList.vue';
import { tempSlidesData } from '../api';
</script>

<template>
  <h1>Slides</h1>
  <section class="slides">
    <DraggableList :items="tempSlidesData" :item-width="'200px'" :item-height="'130px'" />
  </section>
</template>

<style scoped>
.slides {
  height: 80vh;
  overflow-y: auto;
  border: 1px solid rgba(126, 126, 126, 0.705);
}
</style>
