<template>
    <div class="qrcode-container" ref="qrcodeContainer">
    </div>
</template>

<script lang="ts" setup>
import QRCodeStyling, { Options } from 'qr-code-styling';
import { ref, onMounted, watch, onBeforeUnmount, computed } from 'vue';
import { useColorMode } from '@vueuse/core';

const qrcodeContainer = ref<HTMLDivElement | null>(null);
let qrCode: QRCodeStyling | null = null;

const mode = useColorMode();

export interface Props {
    value: string,
    color: string,
    darkColor?: string;
    width: number,
    height: number,
    dotType?: 'square' | 'rounded' | 'dots' | 'extra-rounded' | 'classy' | 'classy-rounded',
    cornerSquareType?: 'square' | 'rounded' | 'extra-rounded',
    cornerDotType?: 'square' | 'dot',
}

const props = withDefaults(defineProps<Props>(), {
    width: 200,
    height: 200,
    color: "000000",
    darkColor: "ffffff",
    dotType: 'rounded',
    cornerSquareType: 'extra-rounded',
    cornerDotType: 'square',
});
const darkenHexColor = (hex: string, percent: number): string | null => {
    const validatedPercent = Math.max(0, Math.min(100, percent));
    const factor = (100 - validatedPercent) / 100;

    const cleanHex = hex.startsWith('#') ? hex.slice(1) : hex;

    let fullHex = cleanHex;
    if (cleanHex.length === 3) {
        fullHex = cleanHex[0] + cleanHex[0] + cleanHex[1] + cleanHex[1] + cleanHex[2] + cleanHex[2];
    }

    if (fullHex.length !== 6 || !/^[0-9a-fA-F]{6}$/.test(fullHex)) {
        console.error('Invalid color format:', hex);
        return null;
    }

    let r = parseInt(fullHex.slice(0, 2), 16);
    let g = parseInt(fullHex.slice(2, 4), 16);
    let b = parseInt(fullHex.slice(4, 6), 16);

    r = Math.round(r * factor);
    g = Math.round(g * factor);
    b = Math.round(b * factor);

    r = Math.max(0, Math.min(255, r));
    g = Math.max(0, Math.min(255, g));
    b = Math.max(0, Math.min(255, b));

    const toHex = (c: number) => ('0' + c.toString(16)).slice(-2);

    return toHex(r) + toHex(g) + toHex(b);
};

const currentColor = computed(() => {
    return mode.value === 'dark' && props.darkColor !== undefined ? props.darkColor : props.color;
});

const offsetColor = computed<string>(() => {
    const darkenPercentage = 85;
    const darkenedHex = darkenHexColor(currentColor.value, darkenPercentage);

    if (darkenedHex === null) {
        return '000000';
    }

    return darkenedHex;
});


const createOptions = (currentValue: string): Options => {
    return {
        data: currentValue,
        width: props.width,
        height: props.height,
        type: 'svg',
        margin: 10,
        dotsOptions: {
            type: props.dotType,
            gradient: {
                type: 'linear',
                rotation: 0,
                colorStops: [
                    {
                        offset: 0,
                        color: '#' + currentColor.value
                    },
                    {
                        offset: 1,
                        color: '#' + offsetColor.value
                    }
                ]
            }
        },
        backgroundOptions: {
            color: 'transparent',
        },
        cornersSquareOptions: {
            type: props.cornerSquareType,
            color: '#' + currentColor.value
        },
        cornersDotOptions: {
            type: props.cornerDotType,
            color: '#' + currentColor.value
        },
        imageOptions: {
            hideBackgroundDots: true,
            crossOrigin: 'anonymous',
        },
    };
};

onMounted(() => {
    if (qrcodeContainer.value) {
        const options = createOptions(props.value);
        qrCode = new QRCodeStyling(options);
        qrCode.append(qrcodeContainer.value);
    }
});

watch(
    () => ({
        value: props.value,
        width: props.width,
        height: props.height,
        color: props.color,
        darkColor: props.darkColor,
        dotType: props.dotType,
        cornerSquareType: props.cornerSquareType,
        cornerDotType: props.cornerDotType,
        mode: mode.value,
    }),
    ({ value: newValue }) => {
        if (qrCode) {
            const options = createOptions(newValue);
            qrCode.update(options);
        } else if (qrcodeContainer.value) {
            const options = createOptions(newValue);
            qrCode = new QRCodeStyling(options);
            qrCode.append(qrcodeContainer.value);
        }
    },
    { deep: true }
);

onBeforeUnmount(() => {
    if (qrCode) {
        qrCode = null;
    }
});

</script>

<style scoped>
.qrcode-container {
    display: inline-block;
    padding: 10px;
    background-color: #FFFFFF;
    transition: background-color 0.3s ease;
}

.dark .qrcode-container {
    background-color: #EDEDED;
}
</style>