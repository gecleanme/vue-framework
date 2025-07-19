<template>
    <fieldset class="py-3" :id="id">
        <legend class="sr-only">
            {{ label }}
        </legend>
        <div class="flex items-center py-1">
            <label
                :class="[{ 'text-red-400': errorMessage.length }]"
                class="text-gray-500 text-md px-1 select-none"
                >{{ label }}</label
            >
        </div>
        <div class="gap-5" :class="[row ? 'flex' : 'flex flex-col']">
            <div
                v-for="item in items"
                :key="item.id"
                class="relative flex items-start"
            >
                <div class="flex items-center h-5">
                    <input
                        @change="$emit('update:modelValue', item[itemKey])"
                        :id="`${id}${item[itemKey]}`"
                        :aria-describedby="`${id}${item[itemKey]}-description`"
                        :name="id"
                        type="radio"
                        :checked="item[itemKey] === modelValue"
                        class="h-4 w-4 border-gray-300"
                        :class="[
                            item.style ??
                                'focus:ring-primary-500 text-primary-600',
                        ]"
                    />
                </div>
                <div class="ltr:ml-3 rtl:mr-3 text-sm">
                    <label
                        :for="`${id}${item[itemKey]}`"
                        class="font-medium text-gray-700"
                        >{{ item[itemName] }}</label
                    >
                    <span
                        :id="`${id}${item[itemKey]}-description`"
                        class="text-gray-500"
                        >{{ item.description }}</span
                    >
                </div>
            </div>
        </div>
        <div class="break-words select-none">
            <p
                v-if="errorMessage.length"
                class="text-sm text-red-400 px-1 mt-1"
            >
                {{ errorMessage }}
            </p>
        </div>
    </fieldset>
</template>

<script setup>
import { useGenerateRandomId } from "@/Composables/generateRandomId.js";
defineOptions({
    name: "RadioGroup",
});

defineProps({
    label: { type: String, default: null, required: false },
    items: { type: Array, default: () => [], required: false },
    modelValue: { default: null, required: false },
    row: { type: Boolean, default: false, required: false },
    errorMessage: { type: String, default: "", required: false },
    id: { type: String, default: () => useGenerateRandomId(), required: false },
    itemKey: { type: String, default: "id", required: false },
    itemName: { type: String, default: "name", required: false },
});

defineEmits(["update:modelValue"]);
</script>

<style scoped></style>
