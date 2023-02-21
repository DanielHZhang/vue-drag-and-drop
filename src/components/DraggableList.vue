<script setup lang="ts">
import { ref } from 'vue';

type Item = {
  id: string;
  title: string;
};

const props = defineProps<{ items: Item[] }>();
const items = ref(props.items);

function onDragStart(event: DragEvent, sourceIndex: number) {
  console.log('drag start:', event);

  if (event.dataTransfer) {
    event.dataTransfer.setData('id', sourceIndex.toString());
  }
}

function onDrop(event: DragEvent, targetIndex: number) {
  console.log('drop:', event);

  if (!event.dataTransfer) {
    return;
  }

  let sourceIndex = parseInt(event.dataTransfer.getData('id'), 10);

  if (isNaN(sourceIndex) || sourceIndex === targetIndex) {
    return; // Do not handle drop events when there is no change in indices
  }

  const sourceItem = items.value[sourceIndex];
  items.value.splice(targetIndex, 0, sourceItem); // Insert source item at target index

  console.log(sourceIndex, targetIndex);
  if (targetIndex < sourceIndex) {
    sourceIndex += 1; // Moved item from larger index to smaller index all indices >targetIndex to shift by 1
  }

  const deleted = items.value.splice(sourceIndex, 1);
  if (deleted.length === 0) {
    console.error('Failed to delete drag source item');
  }
  console.log(items.value);
}

function onDragOver(event: DragEvent) {
  event.preventDefault();
}
</script>

<template>
  <ol class="drag-list">
    <li
      class="drop-zone"
      v-for="(item, index) in items"
      @dragstart="onDragStart($event, index)"
      @drop="onDrop($event, index + 1)"
      @dragover="onDragOver"
    >
      <div class="drag-item" draggable="true">{{ item.title }}</div>
    </li>
  </ol>
</template>

<style scoped>
.drag-list {
  list-style-type: none;
  padding: 0;
  margin: 0;
}

.drop-zone {
  padding: 12px 0;
}

.drag-item {
  padding: 16px;
  border-radius: 8px;
  border: 2px solid black;
  user-select: none;
  cursor: grab;
  background-color: #283649;
  position: relative;
}

.drag-item:not(:last-child) {
  margin-bottom: 16px;
}
</style>
