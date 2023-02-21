<script setup lang="ts">
import DraggableList from '../components/DraggableList.vue';
import { tempSlidesData } from '../api';
</script>

<template>
  <div class="wrapper">
    <h1>Slides</h1>
    <DraggableList :items="tempSlidesData" :item-width-px="200" :item-height-px="130" />
  </div>
</template>

<style scoped>
.wrapper {
  padding: 0 2rem;
  flex-grow: 0;
  align-self: flex-start;
}
</style>
