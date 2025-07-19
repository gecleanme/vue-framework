<template>
    <div class="w-full">
        <div class="w-full flex justify-start">
            <label
                v-if="label && label.length"
                :class="[{ 'text-red-400': errorMessage.length }]"
                class="text-gray-500 text-md px-1 select-none"
                >{{ label }}</label
            >
        </div>
        <div
            :class="[
                {
                    'text-field-container--error':
                        errorMessage && errorMessage.length,
                },
                { 'text-field-container--disabled': disabled },
                disabled ? 'bg-gray-50' : 'bg-white',
            ]"
            class="relative flex items-center text-field-container px-2"
        >
            <div
                :class="[
                    errorMessage.length ? 'text-red-400' : 'text-primary-500',
                ]"
                class="pl-3 inline-flex items-center"
                v-if="$slots.prefix"
                @click="onClickPrepend"
            >
                <slot name="prefix">
                    <span class="text-gray-400 text-lg font-semibold">{{
                        prefix
                    }}</span>
                </slot>
            </div>
            <input
                :disabled="disabled"
                :type="password ? 'password' : type"
                :value="modelValue"
                class="block w-full placeholder:text-gray-400 reset--text-field disabled:bg-gray-100"
                v-bind="$attrs"
                @input="handleInput"
            />
            <span
                v-if="clearable && !disabled && modelValue && modelValue.length"
                class="select-none cursor-pointer text-gray-400 inline-flex items-center"
                @click.stop="onClear"
                ><material-icon :size="2">close</material-icon></span
            >
            <span
                v-if="errorMessage && errorMessage.length"
                class="select-none text-red-400 inline-flex items-center"
                ><material-icon :size="2">error</material-icon></span
            >
            <div
                class="select-none cursor-pointer text-gray-400 flex items-center whitespace-nowrap"
                @click="onClickAppend"
            >
                <slot name="append-icon" />
                <slot name="suffix">
                    <span class="text-gray-400 text-lg font-semibold">{{
                        suffix
                    }}</span>
                </slot>
            </div>
        </div>
        <div class="break-words select-none" v-if="!hideDetails">
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
import { arabicToStandardNumber } from "@/Composables/MultiLingual/arabicToStandardNumber.js";
import MaterialIcon from "@/Framework/Icon/MaterialIcon.vue";
defineOptions({
    name: "NumberField",
    inheritAttrs: false,
});
const props = defineProps({
    errorMessage: { type: String, default: "", required: false },
    type: { type: String, default: "text", required: false },
    label: { type: String, default: null, required: false },
    modelValue: { default: null, required: false },
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
    suffix: { type: String, default: "", required: false },
    password: { type: Boolean, default: false, required: false },
    clearable: { type: Boolean, default: false, required: false },
    disabled: { type: Boolean, default: false, required: false },
    hideDetails: { type: Boolean, default: false, required: false },
    minValue: { type: Number, default: null, required: false },
    maxValue: { type: Number, default: null, required: false },
});

const emit = defineEmits(["update:modelValue", "onClear"]);

function handleInput(event) {
    const value = event.target.value;
    let standardValue = arabicToStandardNumber(value);

    if (standardValue !== "") {
        let numericValue = parseFloat(standardValue);

        if (props.minValue !== null && numericValue < props.minValue) {
            numericValue = props.minValue;
        }

        if (props.maxValue !== null && numericValue > props.maxValue) {
            numericValue = props.maxValue;
        }

        event.target.value = numericValue;
        emit("update:modelValue", numericValue);
    } else {
        event.target.value = props.minValue !== null ? props.minValue : 0;
        emit("update:modelValue", 0);
    }
}
function onClear() {
    emit("update:modelValue", null);
    emit("onClear");
}
function onClickPrepend() {
    props.onPrepend();
}

function onClickAppend() {
    props.onAppend();
}
</script>

<style scoped>
@reference "../../../css/app.css";

.reset--text-field {
    @apply duration-300 rounded-lg shadow-none  outline-hidden border-none focus:ring-0 focus:border-none;
}

.text-field-container {
    @apply transition-all text-gray-600 duration-300 rounded-lg shadow-sm ring-1 outline-hidden ring-primary-300 focus-within:ring-2 focus-within:ring-primary-400 focus-within:border-primary-400;
}

.text-field-container--error {
    @apply ring-red-300 focus-within:ring-red-400 focus-within:border-red-400;
}

.text-field-container--disabled {
    @apply ring-gray-300 focus-within:ring-gray-400 focus-within:border-gray-400 bg-gray-100;
}
</style>
