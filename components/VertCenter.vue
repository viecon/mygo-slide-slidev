<template>
  <div class="vert-center" :style="{ height }">
    <div class="vert-center-inner">
      <slot />
    </div>
  </div>
</template>

<script setup>
defineProps({
  height: {
    type: String,
    default: "100%", // Full height by default
  },
});
</script>

<style scoped>
.vert-center {
  display: table;
  width: 100%;
}

.vert-center-inner {
  display: table-cell;
  vertical-align: middle;
}
</style>
