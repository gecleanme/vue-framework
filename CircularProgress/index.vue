<template>
    <div class="flex items-center flex-col justify-center">
        <div class="relative flex items-center justify-center">
            <svg
                :class="[{ 'animate-spin': indeterminate }]"
                :width="size"
                :height="size"
                :viewBox="`0 0 ${size} ${size}`"
            >
                <circle
                    class="track"
                    :r="radius"
                    :cx="size / 2"
                    :cy="size / 2"
                    fill="transparent"
                    :stroke-width="strokeWidth"
                />
                <circle
                    :r="radius"
                    :cx="size / 2"
                    :cy="size / 2"
                    fill="transparent"
                    class="animate-fill-progress"
                    :stroke-width="strokeWidth"
                    :stroke-dasharray="circumference"
                    :stroke-dashoffset="dashOffset"
                    :stroke="progressColor"
                    :transform="`rotate(90 ${size / 2} ${size / 2})`"
                    stroke-linecap="round"
                />
            </svg>
            <div class="absolute text-sm inline-flex font-semibold">
                <slot name="label" />
            </div>
        </div>
        <p
            :class="[{ hidden: !$slots.default }]"
            class="mt-3 text-center text-xs"
        >
            <slot />
        </p>
    </div>
</template>

<script setup>
import { computed } from "vue";

defineOptions({
    name: "CircularProgress",
});

const props = defineProps({
    progress: {
        type: Number,
        default: 0,
        validator: (value) => value >= 0 && value <= 100,
    },
    size: {
        type: Number,
        default: 80,
    },
    strokeWidth: {
        type: Number,
        default: 10,
    },
    progressColor: {
        type: String,
        default: null,
    },
    indeterminate: {
        type: Boolean,
        default: false,
    },
});

const radius = computed(() => (props.size - props.strokeWidth) / 2);
const circumference = computed(() => 2 * Math.PI * radius.value);
const dashOffset = computed(() => {
    let progress = props.progress;

    if (props.indeterminate) {
        progress = 20;
    }
    return circumference.value * (1 - progress / 100);
});

const progressColor = computed(() => {
    if (props.progressColor) {
        return props.progressColor;
    }
    if (props.progress < 50) {
        return "#ff0000";
    } else if (props.progress >= 50 && props.progress < 100) {
        return "#ffcc00";
    } else {
        return "#00cc00";
    }
});
</script>

<style scoped>
.track {
    stroke: #e5e7eb;
}
.animate-fill-progress {
    transition: stroke-dashoffset 1s ease-out;
}
</style>
