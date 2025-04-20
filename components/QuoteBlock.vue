<template>
    <blockquote class="quote-block" :style="{ borderColor }">
        <slot />
    </blockquote>
</template>

<script setup>
defineProps({
    borderColor: { type: String, default: 'gray' },
})
</script>

<style scoped>
.quote-block {
    border-left-style: solid;
    border-left-width: 4px;
    padding-left: 0.7em;
    padding-top: 0.2em;
    padding-bottom: 0.2em;
    margin: 1em 0 0.5em 1.5em;
    font-style: italic;
    line-height: 1.5;
}
</style>