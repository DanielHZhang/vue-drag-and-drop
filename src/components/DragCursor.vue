<script setup lang="ts">
import { computed } from 'vue';

type Props = {
  isVisible: boolean;
  positionY: number;
  widthPx: number;
};

const props = withDefaults(defineProps<Props>(), {
  isVisible: false,
  positionY: 0,
});

const heightPx = 2;
const height = `${heightPx}px`;
const overhangPx = 16;
const leftPosition = `${overhangPx / 2}px`;

// Cursor should slightly extend beyond the provided width
const width = computed(() => `${props.widthPx + overhangPx}px`);

// Props position does not take into account height of the cursor bar
const positionY = computed(() => `${props.positionY - heightPx / 2}px`);
</script>

<template>
  <hr class="divider" v-if="props.isVisible" />
</template>

<style scoped>
.divider {
  position: absolute;
  left: v-bind(leftPosition);
  top: v-bind('positionY');
  width: v-bind('width');
  pointer-events: none;
  user-select: none;
  border-style: none;
  border-top: v-bind(height) solid #1face3;
  height: v-bind(height);
  margin: 0;
}
</style>
