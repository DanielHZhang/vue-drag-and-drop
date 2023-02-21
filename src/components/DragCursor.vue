<template>
  <div class="divider">
    <div class="divider-handle" />
    <hr class="divider-bar" />
  </div>
</template>

<style scoped>
.divider {
  display: flex;
  align-items: center;
  position: absolute;
}

.divider-bar {
  /* border: 1px solid white; */
  flex-grow: 1;
  border-style: none;
  border-top: 1px solid white;
  /* width: 100%; */
}

.divider-handle {
  width: 10px;
  height: 10px;
  /* background-color: lightblue; */
  border: 1px solid white;
  border-radius: 50%;
}
</style>
