<script setup lang="ts">
import { computed, ref } from 'vue';
import DragCursor from './DragCursor.vue';

type Props = {
  items: Item[];
  itemWidthPx: number;
  itemHeightPx: number;
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
const itemWidth = computed(() => `${props.itemWidthPx}px`);
const itemHeight = computed(() => `${props.itemHeightPx}px`);

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
    let offsetValue = event.target.offsetTop;

    if (event.offsetY > props.itemHeightPx / 2) {
      // When dragging over bottom half of item, show drag cursor below the target item instead of above
      offsetValue += props.itemHeightPx + parseInt(itemPadding, 10); // CSS strings can be parsed (e.g. 200px -> 200, just JS things)
    }

    state.value.offsetY = offsetValue;
  }
}

function onDragEnd(event: DragEvent) {
  state.value.isDragging = false;
}

function onDrop(event: DragEvent, targetIndex: number) {
  if (!event.dataTransfer) {
    return;
  }
  if (event.target instanceof HTMLElement && event.offsetY > props.itemHeightPx / 2) {
    // When dragging over bottom half of target item, place the source item after the target
    targetIndex += 1;
  }

  let sourceIndex = parseInt(event.dataTransfer.getData('index'), 10);
  if (isNaN(sourceIndex) || sourceIndex === targetIndex) {
    return; // Do not handle drop events when there is no change in indices
  }

  const sourceItem = state.value.items[sourceIndex];
  state.value.items.splice(targetIndex, 0, sourceItem); // Insert source item at target index

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
    <DragCursor :is-visible="state.isDragging" :offset-y="state.offsetY" :width-px="props.itemWidthPx" />
    <li
      class="drop-zone"
      v-for="(item, index) in state.items"
      @dragstart="onDragStart($event, index)"
      @dragover="onDragOver($event, index)"
      @dragend="onDragEnd"
      @drop="onDrop($event, index)"
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
  padding: 16px;
  margin: 0;
  position: relative;
}

.drop-zone:not(:last-child) {
  padding-bottom: v-bind(itemPadding);
}

.drag-item {
  width: v-bind('itemWidth');
  height: v-bind('itemHeight');
  padding: 16px;
  border-radius: 8px;
  user-select: none;
  background-color: #283649;
}
</style>
