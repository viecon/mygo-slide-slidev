<template>
  <div class="invert-dark">
    <slot />
  </div>
</template>

<style scoped>
.dark .invert-dark {
  filter: invert(1) hue-rotate(180deg);
}
</style>
