<template>
    <span
        class="whitespace-nowrap inline-flex px-2 w-fit py-1 text-center items-center font-semibold rounded-md"
        :class="[iconSize[size], colors[color]]"
    >
        <slot name="label">
            {{ label }}
        </slot>
    </span>
</template>

<script setup>
import { ref } from "vue";
defineOptions({
    name: "Pill",
});

defineProps({
    label: { default: null, required: false },
    size: {
        type: Number,
        default: 0,
        validator: function (value) {
            return [0.5, 0, 1, 2, 3, 4, 5, 6, 7].includes(value);
        },
    },
    color: {
        type: String,
        default: "default",
        validator: function (value) {
            return [
                "default",
                "secondary",
                "lighten",
                "light",
                "error-light",
                "primary",
                "primary-lighten",
                "error-lighten",
                "error",
                "info",
                "warning",
                "outlined",
                "primary-outlined",
                "gray-outlined",
                "error-outlined",
                "secondary-outlined",
                "infoLighten",
            ].includes(value);
        },
    },
});

const iconSize = ref({
    0.5: "text-xs py-0.5",
    0: "text-sm py-0.5",
    1: "text-xl py-1",
    2: "text-2xl py-1",
    3: "text-3xl py-2",
    4: "text-4xl py-2",
    5: "text-5xl py-2",
    6: "text-6xl py-2",
    7: "text-7xl py-2",
});

const colors = ref({
    default: "bg-gray-600 text-white",
    secondary: "bg-purple-600 text-white",
    lighten: "bg-gray-400 text-white",
    light: "bg-gray-100 text-gray-500",
    primary: "bg-primary-600 text-white",
    error: "bg-red-600 text-white",
    info: "bg-blue-600 text-white",
    infoLighten: "bg-blue-100 text-blue-800",
    warning: "bg-yellow-600 text-white",
    outlined: "bg-white text-gray-600 ring-1 ring-gray-600",
    "primary-outlined": "bg-white text-primary-600 ring-1 ring-primary-600",
    "error-outlined": "bg-white text-red-600 ring-1 ring-red-600",
    "secondary-outlined": "bg-white text-purple-600 ring-1 ring-purple-600",
    "gray-outlined": "bg-white text-gray-500 ring-1 ring-gray-500",
    "primary-lighten": "bg-primary-50 text-primary-800",
    "error-lighten": "bg-red-50 text-red-800",
    "error-light": "bg-red-50 text-red-800",
});
</script>

<style scoped></style>
