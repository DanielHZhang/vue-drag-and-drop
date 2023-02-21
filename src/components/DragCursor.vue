<script setup lang="ts">
import { computed } from 'vue';

type Props = {
  isVisible: boolean;
  offsetY: number;
  widthPx: number;
};

const props = withDefaults(defineProps<Props>(), {
  isVisible: false,
  offsetY: 0,
});
const overhangPx = 16;
const width = computed(() => `${props.widthPx + overhangPx}px`);
const offsetY = computed(() => `${props.offsetY - 9}px`);
</script>

<template>
  <hr class="divider" v-if="props.isVisible" />
</template>

<style scoped>
.divider {
  position: absolute;
  left: 8px;
  top: v-bind('offsetY');
  width: v-bind('width');
  pointer-events: none;
  user-select: none;
  border-style: none;
  border-top: 3px solid skyblue;
  height: 3px;
  margin: 0;
}
</style>
