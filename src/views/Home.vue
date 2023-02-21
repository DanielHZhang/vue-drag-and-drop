<script setup lang="ts">
import DraggableList from '../components/DraggableList.vue';
import { tempSlidesData } from '../api';
</script>

<template>
  <div class="wrapper">
    <h1>Slides</h1>
    <section class="slides">
      <DraggableList :items="tempSlidesData" :item-width-px="200" :item-height-px="130" />
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
