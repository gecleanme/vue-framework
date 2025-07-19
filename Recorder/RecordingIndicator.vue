<script setup>
import { computed, ref, onMounted, nextTick } from "vue";

const props = defineProps({
    volumeLevel: {},
    multiplier: {
        type: Number,
        default: 2.5,
    },
});

const scaleComputed = computed(() => {
    return Math.min(props.volumeLevel, 0.5) * props.multiplier;
});

const slotRef = ref(null);
const placeholderRef = ref(null);
const containerRef = ref(null);

const moveLabel = async () => {
    await nextTick();
    if (slotRef.value && placeholderRef.value && containerRef.value) {
        const slotRect = slotRef.value.getBoundingClientRect();
        const containerRect = containerRef.value.getBoundingClientRect();

        const centerY = containerRect.height / 2;
        const spacing = slotRect.height / 2 + 30;

        placeholderRef.value.style.position = "absolute";
        placeholderRef.value.style.top = `${centerY + spacing}px`;
        placeholderRef.value.style.left = "50%";
        placeholderRef.value.style.transform = "translateX(-50%)";
    }
};
onMounted(async () => {
    moveLabel();
});

defineExpose({ moveLabel });
</script>

<template>
    <div
        ref="containerRef"
        class="relative flex items-center justify-center w-64 h-64"
    >
        <div
            class="absolute w-64 h-64 bg-red-500 rounded-full opacity-30"
            :style="{
                transform: `scale(${scaleComputed})`,
            }"
        ></div>
        <div
            class="absolute w-48 h-48 bg-red-500 rounded-full opacity-40"
            :style="{
                transform: `scale(${scaleComputed})`,
            }"
        ></div>
        <div
            class="absolute w-32 h-32 bg-red-500 rounded-full opacity-50"
            :style="{
                transform: `scale(${scaleComputed})`,
            }"
        ></div>
        <div
            class="absolute w-16 h-16 bg-red-500 rounded-full opacity-60"
            :style="{
                transform: `scale(${scaleComputed})`,
            }"
        ></div>
        <div class="z-1">
            <div
                ref="slotRef"
                class="flex items-center justify-center aspect-square"
            >
                <slot />
            </div>
            <span ref="placeholderRef">
                <slot name="label" :moveLabel="moveLabel" />
            </span>
        </div>
    </div>
</template>

<style scoped></style>
