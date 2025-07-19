<template>
    <div
        class="w-full bg-gray-100 relative overflow-hidden rounded-md"
        :style="{ height: height }"
    >
        <div
            class="h-full transition-all duration-300 ease-in-out"
            :class="[progressColor]"
            :style="{ width: `${clampedPercentage}%` }"
        ></div>

        <template v-if="!hideLabel">
            <span
                class="absolute inset-0 flex items-center justify-center text-sm font-medium text-gray-700"
                v-if="clampedPercentage < 50"
            >
                {{ clampedPercentage }}%
            </span>

            <span
                class="absolute inset-0 flex items-center justify-center text-sm font-medium text-white"
                v-if="clampedPercentage >= 50"
            >
                {{ clampedPercentage }}%
            </span>
        </template>
    </div>
</template>
<script setup>
import { computed } from "vue";

defineOptions({
    name: "ProgressBar",
});
const props = defineProps({
    progress: {
        type: Number,
        default: 0,
    },
    height: {
        type: String,
        default: "1.5rem",
    },
    hideLabel: {
        type: Boolean,
        default: false,
    },
    progressColor: {
        type: String,
        default: "bg-primary-500",
    },
});

const clampedPercentage = computed(() => {
    return Math.min(100, Math.max(0, Math.round(props.progress)));
});
</script>
