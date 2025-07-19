<template>
    <a
        :class="[colorClass, { 'shadow-md': !depressed }]"
        class="transition-all cursor-pointer uppercase duration-150 min-h-[2rem] select-none font-semibold text-xs py-2 px-4 rounded-sm focus:outline-hidden focus:shadow-outline flex items-center justify-center relative"
    >
        <span
            class="flex items-center gap-2"
            :class="[!loading ? 'opacity-100' : 'opacity-0']"
        >
            <slot />
        </span>

        <svg
            v-if="loading && !progress"
            :class="[loading ? 'opacity-100' : 'opacity-0']"
            class="w-6 h-6 animate-spin absolute"
            viewBox="0 0 32 32"
            xmlns="http://www.w3.org/2000/svg"
        >
            <path
                d="M29.89 15.81a2.51 2.51 0 1 0-5 .45 9.65 9.65 0 0 1-1.68 6.34 10.24 10.24 0 0 1-5.74 4 10.71 10.71 0 0 1-7.38-.7 11.44 11.44 0 0 1-5.48-5.62A12.07 12.07 0 0 0 9.46 27 12.58 12.58 0 0 0 17.9 29a13.31 13.31 0 0 0 8.18-4 14 14 0 0 0 3.81-8.75v-.08A2.29 2.29 0 0 0 29.89 15.81zM7.11 15.74A9.65 9.65 0 0 1 8.79 9.4a10.24 10.24 0 0 1 5.74-4 10.71 10.71 0 0 1 7.38.7 11.44 11.44 0 0 1 5.48 5.62A12.07 12.07 0 0 0 22.54 5 12.58 12.58 0 0 0 14.1 3 13.31 13.31 0 0 0 5.92 7a14 14 0 0 0-3.81 8.75v.08a2.29 2.29 0 0 0 0 .37 2.51 2.51 0 1 0 5-.45z"
                data-name="Looding 19"
                fill="currentColor"
            />
        </svg>
        <span
            v-if="loading && progress"
            :class="[loading && progress ? 'opacity-100' : 'opacity-0']"
            class="px-3 absolute"
        >
            {{ progress }}%
        </span>
    </a>
</template>

<script setup>
import { ref, watchEffect } from "vue";

defineOptions({
    name: "PrimaryLink",
});

const props = defineProps({
    loading: { type: Boolean, default: false, required: false },
    depressed: { type: Boolean, default: false, required: false },
    color: { type: String, default: "primary", required: false },
    variant: { type: String, default: null, required: false },
    progress: { type: Number, default: null, required: false },
});

const colorClass = ref("");

watchEffect(() => {
    if (props.color === "primary") {
        if (props.loading) {
            colorClass.value =
                props.variant === "outlined"
                    ? "text-primary-500 ring-1 ring-primary-500 disabled:ring-gray-300 disabled:text-gray-300"
                    : "ring-primary-500  ring-1 text-primary-500 disabled:ring-gray-300 disabled:text-gray-300";
        } else {
            colorClass.value =
                props.variant === "outlined"
                    ? "text-primary-500 ring-1 ring-primary-500 hover:bg-primary-50 disabled:ring-gray-300 disabled:text-gray-300 disabled:hover:bg-white"
                    : "bg-primary-500 text-white disabled:bg-gray-200 hover:bg-primary-400 focus:bg-primary-400";
        }
    }

    if (props.color === "error") {
        if (props.loading) {
            colorClass.value =
                props.variant === "outlined"
                    ? "text-red-500 ring-1 ring-red-500 disabled:ring-gray-300 disabled:text-gray-300"
                    : "ring-red-500  ring-1 text-red-500 disabled:ring-gray-300 disabled:text-gray-300";
        } else {
            colorClass.value =
                props.variant === "outlined"
                    ? "text-red-500 ring-1 ring-red-500 hover:bg-error-50 disabled:ring-gray-300 disabled:text-gray-300 disabled:hover:bg-white"
                    : "bg-red-500 text-white disabled:bg-gray-200 hover:bg-error-400 focus:bg-red-400 hover:bg-red-400";
        }
    }

    if (props.color === "secondary") {
        if (props.loading) {
            colorClass.value =
                props.variant === "outlined"
                    ? "text-purple-500 ring-1 ring-purple-500 disabled:ring-gray-300 disabled:text-gray-300"
                    : "ring-purple-500  ring-1 text-purple-500 disabled:ring-gray-300 disabled:text-gray-300";
        } else {
            colorClass.value =
                props.variant === "outlined"
                    ? "text-purple-500 ring-1 ring-purple-500 hover:bg-purple-50 disabled:ring-gray-300 disabled:text-gray-300 disabled:hover:bg-white"
                    : "bg-purple-500 text-white disabled:bg-gray-200 hover:bg-purple-400 focus:bg-purple-400";
        }
    }
    if (props.color === "warning") {
        if (props.loading) {
            colorClass.value =
                props.variant === "outlined"
                    ? "text-yellow-500 ring-1 ring-yellow-500 disabled:ring-gray-300 disabled:text-gray-300"
                    : "ring-yellow-500  ring-1 text-yellow-500 disabled:ring-gray-300 disabled:text-gray-300";
        } else {
            colorClass.value =
                props.variant === "outlined"
                    ? "text-yellow-500 ring-1 ring-yellow-500 hover:bg-yellow-50 disabled:ring-gray-300 disabled:text-gray-300 disabled:hover:bg-white"
                    : "bg-yellow-500 text-white disabled:bg-gray-200 hover:bg-yellow-400 focus:bg-yellow-400";
        }
    }

    if (props.color === "white") {
        if (props.loading) {
            colorClass.value =
                props.variant === "outlined"
                    ? "bg-white text-gray-600 disabled:ring-gray-300 disabled:text-gray-300 ring-1 ring-gray-500 "
                    : "bg-white text-gray-600 disabled:ring-gray-300 disabled:text-gray-300";
        } else {
            colorClass.value =
                props.variant === "outlined"
                    ? "ring-1 ring-gray-400 bg-white outline-hidden text-gray-700 hover:bg-gray-400 hover:text-white disabled:bg-gray-200 disabled:text-gray-300"
                    : "bg-white outline-hidden text-gray-700 hover:bg-gray-400 hover:text-white disabled:bg-gray-200 disabled:text-gray-300";
        }
    }
});
</script>

<style scoped></style>
