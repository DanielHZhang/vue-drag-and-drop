<script setup lang="ts">
import DraggableList from '../components/DraggableList.vue';
import { tempSlidesData } from '../api';
</script>

<template>
  <div>Test</div>
  <DraggableList :items="tempSlidesData" />
</template>

<style scoped></style>
