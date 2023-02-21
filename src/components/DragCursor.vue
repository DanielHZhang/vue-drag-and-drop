<script setup lang="ts">
import { computed, ref } from 'vue';

type Props = {
  isVisible: boolean;
  offsetY: number;
};

const props = withDefaults(defineProps<Props>(), {
  isVisible: false,
  offsetY: 0,
});
const offsetY = computed(() => `${props.offsetY}px`);
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
}

.divider {
  /* border: 1px solid white; */
  flex-grow: 1;
  width: 100px;
  border-style: none;
  border-top: 1px solid white;
  /* width: 100%; */
}

.mark {
  width: 10px;
  height: 10px;
  /* background-color: lightblue; */
  border: 1px solid white;
  border-radius: 50%;
}
</style>
