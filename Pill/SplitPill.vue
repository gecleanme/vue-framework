<template>
    <span
        class="inline-flex px-2 h-fit text-center items-center divide-x rtl:divide-x-reverse font-semibold rounded-md"
        :class="[iconSize[size], colors[color], styleClass]"
    >
        <span class="px-1">
            <slot name="label">
                {{ label }}
            </slot>
        </span>
        <span class="px-1">
            <slot name="value">
                {{ value }}
            </slot>
        </span>
    </span>
</template>

<script setup>
import { ref } from "vue";

defineOptions({
    name: "SplitPill",
});

defineProps({
    label: {
        type: String,
        default: null,
        required: false,
    },
    value: {
        default: null,
        required: false,
    },
    styleClass: {
        default: null,
        required: false,
    },
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
                "lighten",
                "light",
                "primary",
                "error",
                "info",
                "warning",
                "outlined",
                "primary-outlined",
                "warning-outlined",
                "secondary-outlined",
                "error-outlined",
                "info-outlined",
                "pink-outlined",
                "gray-outlined",
                "gray-light-outlined",
                "gray-lighten",
            ].includes(value);
        },
    },
});

const iconSize = ref({
    0.5: "text-xs",
    0: "text-sm",
    1: "text-xl",
    2: "text-2xl",
    3: "text-3xl",
    4: "text-4xl",
    5: "text-5xl",
    6: "text-6xl",
    7: "text-7xl",
});

const colors = ref({
    default: "bg-gray-500 text-white",
    secondary: "bg-purple-600 text-white",
    lighten: "bg-gray-400 text-white",
    primary: "bg-primary-600 text-white",
    error: "bg-red-600 text-white",
    info: "bg-blue-600 text-white",
    infoLighten: "bg-blue-100 text-blue-800",
    primaryLighten: "bg-primary-100 text-primary-800",
    warningLighten: "bg-yellow-100 text-yellow-800",
    errorLighten: "bg-red-100 text-red-800",
    secondaryLighten: "bg-purple-100 text-purple-800",
    warning: "bg-yellow-600 text-white",
    outlined: "bg-white text-gray-600 ring-1 ring-gray-600",
    light: "bg-gray-100 text-gray-500",
    "primary-outlined": "bg-white text-primary-600 ring-1 ring-primary-600",
    "info-outlined": "bg-white text-blue-600 ring-1 ring-blue-600",
    "error-outlined": "bg-white text-red-600 ring-1 ring-red-600",
    "pink-outlined": "bg-white text-pink-600 ring-1 ring-pink-600",
    "secondary-outlined": "bg-white text-purple-600 ring-1 ring-purple-600",
    "warning-outlined": "bg-white text-yellow-700 ring-1 ring-yellow-700",
    "gray-outlined": "bg-white text-gray-700 ring-1 ring-gray-700",
    "gray-light-outlined": "bg-white text-gray-400 ring-1 ring-gray-300",
    "gray-lighten": "bg-gray-50 text-gray-800",
    grayLighten: "bg-gray-50 text-gray-800",
});
</script>

<style scoped></style>
