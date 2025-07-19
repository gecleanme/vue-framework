<template>
    <div class="w-full">
        <div class="w-full gap-3 flex">
            <slot name="label">
                <label
                    v-if="label && label.length"
                    :class="[{ 'text-red-400': errorMessage.length }]"
                    class="text-gray-500 text-md px-1 select-none"
                    >{{ label }}
                </label>
            </slot>
            <slot name="title-append" />
        </div>
        <div class="flex flex-col px-1">
            <slot />
        </div>
        <div class="break-words text-gray-400 select-none">
            <p v-if="hint.length" class="text-sm px-1 mt-1">
                {{ hint }}
            </p>
        </div>
        <div class="break-words select-none">
            <p
                v-if="errorMessage.length"
                class="text-sm text-red-400 px-1 mt-1"
            >
                {{ errorMessage }}
            </p>
        </div>
    </div>
</template>

<script setup>
defineOptions({
    name: "InputGroup",
});

defineProps({
    errorMessage: { type: String, default: "", required: false },
    label: { type: String, default: null, required: false },
    hint: { type: String, default: "", required: false },
});
</script>

<style scoped></style>
