<script setup lang="ts">
import { ref } from 'vue';
import DragCursor from './DragCursor.vue';

type Props = {
  items: Item[];
  itemWidth: string;
  itemHeight: string;
};

type Item = {
  id: string;
  title: string;
};

const props = defineProps<Props>();
const items = ref(props.items);
const isDragging = ref(false);
const offsetY = ref(0);

function onDragStart(event: DragEvent, sourceIndex: number) {
  isDragging.value = true;

  if (event.dataTransfer) {
    event.dataTransfer.setData('id', sourceIndex.toString());
  }
}

function onDragOver(event: DragEvent, index: number) {
  event.preventDefault();

  if (event.target instanceof HTMLElement) {
    console.log('index', index, event.target.offsetTop);
    offsetY.value = event.target.offsetTop;
    //
  }
}

function onDragEnd(event: DragEvent) {
  isDragging.value = false;
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
</script>

<template>
  <ol class="drag-list">
    <DragCursor :isVisible="isDragging" :offsetY="offsetY" />
    <li
      class="drop-zone"
      v-for="(item, index) in items"
      @dragstart="onDragStart($event, index)"
      @dragover="onDragOver($event, index)"
      @dragend="onDragEnd"
      @drop="onDrop($event, index + 1)"
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
  position: relative;
}

.drop-zone {
  padding: 12px 0;
}

.drag-item {
  width: v-bind('props.itemWidth');
  height: v-bind('props.itemHeight');
  /* padding: 16px; */
  border-radius: 8px;
  /* border: 2px solid black; */
  user-select: none;
  cursor: grab;
  background-color: #283649;
}

.drag-item:not(:last-child) {
  margin-bottom: 16px;
}
</style>
