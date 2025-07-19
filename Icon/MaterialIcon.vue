<template>
    <span :class="[iconSize[size]]" class="inline-flex h-fit">
        <span
            :class="[
                colors[color],
                rotates[rotate],
                { 'opacity-0': placeholderMode },
            ]"
            class="material-symbols-outlined"
        >
            <slot v-if="!placeholderMode">
                {{ icon }}
            </slot>
            <template v-else> fiber_manual_record </template>
        </span>
    </span>
</template>

<script setup>
import { ref } from "vue";

defineOptions({
    name: "MaterialIcon",
});
defineProps({
    icon: {
        type: String,
        default: null,
    },
    placeholderMode: {
        type: Boolean,
        default: false,
    },
    size: {
        type: Number,
        default: 3,
        validator: function (value) {
            return [0.5, 1, 2, 3, 4, 5, 6, 7].includes(value);
        },
    },
    color: {
        type: String,
        default: "none",
        validator: function (value) {
            return [
                "default",
                "primary",
                "warning",
                "error",
                "blue",
                "white",
                "gray",
                "gray-lighten",
                "none",
            ].includes(value);
        },
    },
    rotate: {
        type: String,
        default: "default",
        validator: function (value) {
            return ["default", "right", "down", "left"].includes(value);
        },
    },
});

const iconSize = ref({
    0.5: "text-lg",
    1: "text-xl",
    2: "text-2xl",
    3: "text-3xl",
    4: "text-4xl",
    5: "text-5xl",
    6: "text-6xl",
    7: "text-7xl",
});

const colors = ref({
    default: "text-gray-600",
    primary: "text-primary-500",
    warning: "text-yellow-600",
    error: "text-red-500",
    blue: "text-blue-500",
    white: "text-white",
    gray: "text-gray-500",
    "gray-lighten": "text-gray-300",
    none: "",
});
const rotates = ref({
    default: "",
    right: "rotate-90",
    down: "rotate-180",
    left: "-rotate-90",
});
</script>

<style>
.material-symbols-outlined {
    font-variation-settings:
        "FILL" 0,
        "wght" 400,
        "GRAD" 0,
        "OPSZ" 40;
    font-size: inherit;
}
</style>
