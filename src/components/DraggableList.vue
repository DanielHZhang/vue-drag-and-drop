<script setup lang="ts">
import { computed, onMounted, onUnmounted, ref } from 'vue';
import DragCursor from './DragCursor.vue';

type Item = {
  id: string;
  title: string;
};

type HistoryItem = {
  prevIndex: number;
  currentIndex: number;
  data: Item;
};

type Props = {
  items: Item[];
  itemWidthPx: number;
  itemHeightPx: number;
};

type State = {
  items: Item[];
  cursorPositionY: number;
  isDragging: boolean;
  isScrolling: boolean;
  undoStack: HistoryItem[];
  redoStack: HistoryItem[];
  selectedIndex: number;
};

const props = defineProps<Props>();
const state = ref<State>({
  items: props.items,
  cursorPositionY: 0,
  isDragging: false,
  isScrolling: false,
  selectedIndex: 0,
  undoStack: [],
  redoStack: [],
});
const dragSectionElement = ref<HTMLDivElement | null>(null);

const itemPaddingBottomPx = 16;
const itemPaddingBottom = `${itemPaddingBottomPx}px`;
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
    const positionY = event.target.offsetTop; // Position of the top left corner of the hovered target item.
    const paddingCenter = itemPaddingBottomPx / 2;

    if (event.offsetY < props.itemHeightPx / 2) {
      // When dragging over top half of item, show drag cursor above the target item. The cursor should appear in the
      // center of the padding that separates two items.
      state.value.cursorPositionY = positionY - paddingCenter;
    } else {
      // When dragging over bottom half of item, show drag cursor below the target item by adjusting for the item's height.
      state.value.cursorPositionY = positionY + props.itemHeightPx + paddingCenter;
    }
  }
}

function onDragEnd(event: DragEvent) {
  state.value.isDragging = false;
}

function onDrop(event: DragEvent, targetIndex: number) {
  if (!event.dataTransfer) {
    return;
  }

  const originalIndex = parseInt(event.dataTransfer.getData('index'), 10);
  if (
    isNaN(originalIndex) ||
    originalIndex < 0 ||
    originalIndex > state.value.items.length ||
    originalIndex === targetIndex
  ) {
    return; // Do not handle drop events when there is no change in indices
  }
  if (targetIndex < originalIndex && event.target instanceof HTMLElement && event.offsetY > props.itemHeightPx / 2) {
    // When dragging over bottom half of target item, place the source item after the target
    targetIndex += 1;
  }

  // Delete item from original index
  const deleted = state.value.items.splice(originalIndex, 1);
  state.value.items.splice(targetIndex, 0, deleted[0]); // Insert source item at target index
  state.value.selectedIndex = targetIndex;
  state.value.undoStack.push({ prevIndex: originalIndex, currentIndex: targetIndex, data: deleted[0] });
  state.value.redoStack.length = 0; // Clear the redo stack
}

function onDragOverSection(event: DragEvent) {
  if (!dragSectionElement.value || state.value.isScrolling) {
    return;
  }

  const scrollBufferZonePx = 50;
  const scrollAmountPx = 100;
  const { top, bottom } = dragSectionElement.value.getBoundingClientRect();

  // Debounce the scroll because the "smooth" behaviour uses an easing curve that takes some milliseconds to complete
  const debouncedScroll = (amount: number) => {
    dragSectionElement.value!.scrollBy({ top: amount, behavior: 'smooth' });
    state.value.isScrolling = true;
    setTimeout(() => {
      state.value.isScrolling = false;
    }, 80);
  };

  if (event.clientY > bottom - scrollBufferZonePx) {
    // Within bottom scroll buffer zone -> scroll down
    debouncedScroll(scrollAmountPx);
  } else if (event.clientY < top + scrollBufferZonePx) {
    // Within top scroll buffer zone -> scroll up
    debouncedScroll(-scrollAmountPx);
  }
}

function undo() {
  const undoItem = state.value.undoStack.pop();
  if (!undoItem) {
    return;
  }

  console.log(undoItem);
  if (undoItem.currentIndex > undoItem.prevIndex) {
    state.value.items.splice(undoItem.currentIndex, 1);
    state.value.items.splice(undoItem.prevIndex, 0, undoItem.data);
  } else {
    state.value.items.splice(undoItem.prevIndex, 0, undoItem.data);
    state.value.items.splice(undoItem.currentIndex, 1);
  }
}

function documentKeyDown(event: KeyboardEvent) {
  if (event.key === 'z' && (event.metaKey || event.ctrlKey)) {
    if (event.shiftKey) {
      // Handle redo
      const previous = state.value.redoStack.pop();
    } else {
      undo();
    }
  }
}

function onMouseDown(index: number) {
  state.value.selectedIndex = index;
}

onMounted(() => {
  document.addEventListener('keydown', documentKeyDown);
});

onUnmounted(() => {
  document.removeEventListener('keydown', documentKeyDown);
});
</script>

<template>
  <section class="drag-section" @dragover="onDragOverSection" ref="dragSectionElement">
    <ol class="drag-list">
      <DragCursor :is-visible="state.isDragging" :position-y="state.cursorPositionY" :width-px="props.itemWidthPx" />
      <li
        class="drop-zone"
        v-for="(item, index) in state.items"
        @dragstart="onDragStart($event, index)"
        @dragover="onDragOver($event, index)"
        @dragend="onDragEnd"
        @drop="onDrop($event, index)"
        @mousedown="onMouseDown(index)"
      >
        <div class="drag-item" :class="{ selected: state.selectedIndex === index }" draggable="true">
          {{ item.title }}
        </div>
      </li>
    </ol>
  </section>
</template>

<style scoped>
.drag-section {
  height: 80vh;
  overflow-y: auto;
}

.drag-list {
  list-style-type: none;
  padding: 16px;
  margin: 0;
  position: relative;
}

.drop-zone:not(:last-child) {
  padding-bottom: v-bind(itemPaddingBottom);
}

.selected {
  border-style: inset;
  border: 2px solid #1face3;
}

.drag-item {
  display: flex;
  justify-content: center;
  align-items: center;
  width: v-bind('itemWidth');
  height: v-bind('itemHeight');
  padding: 16px;
  border-radius: 8px;
  user-select: none;
  background-color: #414141;
}

@media (prefers-color-scheme: light) {
  .drag-item {
    background-color: #d3d3d3;
  }
}
</style>
