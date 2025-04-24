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

const currentColor = computed(() => {
    return mode.value === 'dark' && props.darkColor !== undefined ? props.darkColor : props.color;
});

const offsetColor = computed<string>(() => {
    const percent = 85;
    const hexColor = currentColor.value.startsWith('#') ? currentColor.value.slice(1) : currentColor.value;
    if (hexColor.length !== 6) {
        console.error('Invalid color format', currentColor.value);
        return '000000';
    }

    let r = parseInt(hexColor.slice(0, 2), 16),
        g = parseInt(hexColor.slice(2, 4), 16),
        b = parseInt(hexColor.slice(4, 6), 16);

    r = Math.round(r * (100 - percent) / 100);
    g = Math.round(g * (100 - percent) / 100);
    b = Math.round(b * (100 - percent) / 100);

    return ('0' + (r || 0).toString(16)).slice(-2) +
        ('0' + (g || 0).toString(16)).slice(-2) +
        ('0' + (b || 0).toString(16)).slice(-2);
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