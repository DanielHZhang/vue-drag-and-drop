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
const state = ref({
  items: props.items,
  isDragging: false,
  offsetY: 0,
});

const itemPadding = '16px';

function onDragStart(event: DragEvent, sourceIndex: number) {
  state.value.isDragging = true;

  if (event.dataTransfer) {
    event.dataTransfer.dropEffect = 'move';
    event.dataTransfer.effectAllowed = 'move';
    event.dataTransfer.setData('index', sourceIndex.toString());
  }
}

function onDragOver(event: DragEvent, index: number) {
  event.preventDefault(); // Allows onDrop to trigger consistently

  if (event.target instanceof HTMLElement) {
    const itemHeight = parseInt(props.itemHeight, 10); // CSS strings can be parsed (e.g. 200px -> 200, just JS things)
    let offsetValue = event.target.offsetTop;

    if (event.offsetY > itemHeight / 2) {
      // When dragging over bottom half of item, show drag cursor below the target item instead of above
      offsetValue += itemHeight + parseInt(itemPadding, 10);
    }

    state.value.offsetY = offsetValue;
  }
}

function onDragEnd(event: DragEvent) {
  state.value.isDragging = false;
}

function onDrop(event: DragEvent, targetIndex: number) {
  console.log('drop:', event);

  if (!event.dataTransfer) {
    return;
  }

  let sourceIndex = parseInt(event.dataTransfer.getData('index'), 10);

  if (isNaN(sourceIndex) || sourceIndex === targetIndex) {
    return; // Do not handle drop events when there is no change in indices
  }

  const sourceItem = state.value.items[sourceIndex];
  state.value.items.splice(targetIndex, 0, sourceItem); // Insert source item at target index

  // TODO: targetIndex + 1 depending on offset of dragover
  if (targetIndex < sourceIndex) {
    sourceIndex += 1; // Moved item from larger index to smaller index all indices >targetIndex to shift by 1
  }

  const deleted = state.value.items.splice(sourceIndex, 1);
  if (deleted.length === 0) {
    console.error('Failed to delete drag source item');
  }
}
</script>

<template>
  <ol class="drag-list">
    <DragCursor :isVisible="state.isDragging" :offsetY="state.offsetY" :width="props.itemWidth" />
    <li
      class="drop-zone"
      v-for="(item, index) in state.items"
      @dragstart="onDragStart($event, index)"
      @dragover="onDragOver($event, index)"
      @dragend="onDragEnd"
      @drop="onDrop($event, index + 1)"
    >
      <div class="drag-item" draggable="true">
        {{ item.title }}
      </div>
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

.drop-zone:not(:last-child) {
  padding-bottom: v-bind(itemPadding);
}

.drag-item {
  width: v-bind('props.itemWidth');
  height: v-bind('props.itemHeight');
  padding: 16px;
  border-radius: 8px;
  user-select: none;
  /* cursor: grab; */
  background-color: #283649;
}
</style>
