<template>
  <div class="invert-dark">
    <slot />
  </div>
</template>

<style scoped>
.invert-dark {
  /* Only invert in dark mode */
  /* Slidev applies the class 'dark' to the <html> tag */
}

.dark .invert-dark {
  filter: invert(1) hue-rotate(180deg);
}
</style>
