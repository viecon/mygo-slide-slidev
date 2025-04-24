<template>
    <div :style="containerStyle">
        <slot></slot>
    </div>
</template>

<script setup>
import { computed } from 'vue';

// 定義元件接收的屬性 (props)
const props = defineProps({
    // fontSize 屬性用來設定字體大小
    // 可以是數字 (預設會加上 'px') 或字串 (如 '1em', '16px', 'small' 等)
    fontSize: {
        type: [String, Number],
        default: '1em' // 預設字體大小為 1em
    }
});

// 根據 props.fontSize 計算並產生要應用的樣式物件
const containerStyle = computed(() => {
    // 如果傳入的是數字，我們將其轉換為帶有 'px' 的字串
    const size = typeof props.fontSize === 'number' ? `${props.fontSize}px` : props.fontSize;
    return {
        fontSize: size,
    };
});
</script>

<style scoped>
/*
    使用 :deep() 選取器來深入到 slot 內容中的元素
    Slidev 預設的程式碼區塊通常是 <pre> 包含 <code> 標籤
    我們在這裡強制將這些元素的 font-size 設定為 inherit，
    這樣它們就會繼承父層 div 的 font-size 樣式。
    使用 !important 是為了確保覆蓋 Slidev 或 PrismJS 的預設樣式。
  */
div :deep(pre),
div :deep(code) {
    font-size: inherit !important;
}
</style>