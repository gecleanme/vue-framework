<template>
    <div class="w-full">
        <div class="flex w-full">
            <label
                v-if="label && label.length"
                :class="[{ 'text-red-400': errorMessage.length }]"
                class="text-gray-500 text-md px-1 select-none"
                >{{ label }}</label
            >
        </div>
        <div class="relative flex items-center rtl:flex-row-reverse">
            <div
                :class="[
                    errorMessage.length ? 'text-red-400' : 'text-primary-500',
                ]"
                class="absolute pl-3"
                @click="onClickPrepend"
            >
                <slot name="prefix">
                    <span class="text-gray-400 text-lg font-semibold">{{
                        prefix
                    }}</span>
                </slot>
            </div>
            <textarea
                :class="[
                    {
                        'border-red-300  focus:ring-red-300 focus:border-red-400':
                            errorMessage.length,
                    },
                    $slots['append-icon'] && errorMessage.length
                        ? 'ltr:pr-20 rtl:pl-20'
                        : 'ltr:pr-10 rtl:pl-10',
                    { 'pl-10': prefix.length || $slots['prefix'] },
                ]"
                :value="modelValue"
                class="block w-full disabled:border-gray-300"
                v-bind="$attrs"
                @input="$emit('update:modelValue', $event.target.value)"
            />
            <div
                :class="[{ 'py-1.5': errorMessage.length }]"
                class="absolute inset-y-0 ltr:right-0 rtl:left-0 flex ltr:pr-1.5 rtl:pl-1.5 items-center gap-x-1"
            >
                <svg
                    v-if="errorMessage.length"
                    aria-hidden="true"
                    class="h-5 w-5 text-red-400"
                    fill="currentColor"
                    viewBox="0 0 20 20"
                    xmlns="http://www.w3.org/2000/svg"
                >
                    <path
                        clip-rule="evenodd"
                        d="M18 10a8 8 0 11-16 0 8 8 0 0116 0zm-7 4a1 1 0 11-2 0 1 1 0 012 0zm-1-9a1 1 0 00-1 1v4a1 1 0 102 0V6a1 1 0 00-1-1z"
                        fill-rule="evenodd"
                    />
                </svg>
                <div
                    class="cursor-pointer text-gray-500"
                    @click="onClickAppend"
                >
                    <slot name="append-icon" />
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
    </div>
</template>

<script setup>
defineOptions({
    name: "TextArea",
    inheritAttrs: false,
});

const props = defineProps({
    errorMessage: { type: String, default: "", required: false },
    label: { type: String, default: null, required: false },
    modelValue: { type: String, default: null, required: false },
    onAppend: {
        type: Function,
        default: () => {},
        required: false,
    },
    onPrepend: {
        type: Function,
        default: () => {},
        required: false,
    },
    prefix: { type: String, default: "", required: false },
    clearable: { type: Boolean, default: false, required: false },
});

defineEmits(["update:modelValue"]);

function onClickPrepend() {
    props.onPrepend();
}

function onClickAppend() {
    props.onAppend();
}
</script>

<style scoped>
*::-webkit-scrollbar {
    width: 0.2em !important;
    height: 0.2em !important;
}

*::-webkit-scrollbar-track {
    -webkit-box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.3) !important;
}

*::-webkit-scrollbar-thumb {
    background-color: #22c55e !important;
    outline: 1px solid #22c55e !important;
}
</style>
