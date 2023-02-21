<script setup lang="ts">
import { computed } from 'vue';

type Props = {
  isVisible: boolean;
  offsetY: number;
  width: string;
};

const props = withDefaults(defineProps<Props>(), {
  isVisible: false,
  offsetY: 0,
});
const offsetY = computed(() => `${props.offsetY - 16}px`);
</script>

<template>
  <div class="drag-cursor">
    <!-- <div class="drag-cursor" v-if="props.isVisible"> -->
    <div class="mark" />
    <hr class="divider" />
  </div>
</template>

<style scoped>
.drag-cursor {
  display: flex;
  align-items: center;
  position: absolute;
  top: v-bind('offsetY');
  pointer-events: none;
  user-select: none;
}

.divider {
  flex-grow: 1;
  width: calc(v-bind('props.width') - 10px);
  border-style: none;
  border-top: 1px solid white;
}

.mark {
  width: 10px;
  height: 10px;
  /* background-color: lightblue; */
  border: 1px solid white;
  border-radius: 50%;
}
</style>
