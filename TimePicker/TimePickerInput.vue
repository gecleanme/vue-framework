<template>
    <div class="w-full">
        <label
            v-if="label && label.length"
            :class="[{ 'text-red-400': errorMessage.length }]"
            class="text-gray-500 text-md px-1 select-none"
            >{{ label }}</label
        >
        <div
            :class="[
                {
                    'text-field-container--error':
                        errorMessage && errorMessage.length,
                },
                { 'text-field-container--disabled': disabled },
            ]"
            class="relative flex items-center rtl:flex-row-reverse text-field-container"
        >
            <div
                :class="[
                    errorMessage.length ? 'text-red-400' : 'text-primary-500',
                ]"
                @click="onClickPrepend"
            >
                <slot name="prefix" :clear="clear">
                    <span class="text-gray-400 text-lg font-semibold">{{
                        prefix
                    }}</span>
                </slot>
            </div>
            <input
                :disabled="disabled"
                ref="datepicker"
                :max="max"
                :min="min"
                :type="type"
                v-model="model"
                class="block w-full placeholder:text-gray-400 reset--text-field"
                v-bind="$attrs"
            />
            <button
                v-if="clearable && !disabled && model && model.length"
                class="select-none cursor-pointer text-gray-400"
                @click="clear"
            >
                <material-icon :size="2">close</material-icon>
            </button>
            <span
                v-if="errorMessage && errorMessage.length"
                class="select-none text-red-400 inline-flex items-center"
                ><material-icon :size="1">error</material-icon></span
            >
            <div
                class="select-none cursor-pointer text-gray-400 flex items-center whitespace-nowrap"
                @click="onClickAppend"
            >
                <slot name="append-icon" :clear="clear" />
            </div>
        </div>
        <div class="break-words select-none">
            <template v-if="errorMessage.length">
                <ul v-if="Array.isArray(errorMessage)">
                    <li
                        class="text-sm text-red-400 px-1 mt-1"
                        v-for="error in errorMessage"
                        :key="error"
                    >
                        {{ error }}
                    </li>
                </ul>
                <p v-else class="text-sm text-red-400 px-1 mt-1">
                    {{ errorMessage }}
                </p>
            </template>
        </div>
        <div class="break-words select-none">
            <slot name="bottom" :clear="clear" />
        </div>
    </div>
</template>

<script setup>
import { ref } from "vue";
import MaterialIcon from "@/Framework/Icon/MaterialIcon.vue";
defineOptions({
    name: "TimePickerInput",
    inheritAttrs: false,
});

const props = defineProps({
    errorMessage: { type: String, default: "", required: false },
    label: { type: String, default: null, required: false },
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
    password: { type: Boolean, default: false, required: false },
    max: { type: String, default: "3099-01-01", required: false },
    type: { type: String, default: "time", required: false },
    min: { type: String, default: null, required: false },
    clearable: { type: Boolean, default: false, required: false },
    disabled: { type: Boolean, default: false, required: false },
});

const model = defineModel();

const datepicker = ref(null);

function onClickPrepend() {
    props.onPrepend();
}

function onClickAppend() {
    props.onAppend();
}

const clear = () => {
    model.value = null;
};
</script>

<style scoped>
@reference "../../../css/app.css";

.reset--text-field {
    @apply duration-300 rounded-lg shadow-none  outline-hidden border-none focus:ring-0 focus:border-none;
}

.text-field-container {
    @apply px-2 transition-all text-gray-600 duration-300 rounded-lg shadow-sm ring-1 outline-hidden ring-primary-300 focus-within:ring-2 focus-within:ring-primary-400 focus-within:border-primary-400;
}

.text-field-container--error {
    @apply ring-red-300 focus-within:ring-red-400 focus-within:border-red-400;
}

.text-field-container--disabled {
    @apply ring-gray-300 focus-within:ring-gray-400 focus-within:border-gray-400;
}
</style>
