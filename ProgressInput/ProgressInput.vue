<template>
    <div class="w-full">
        <label
            v-if="label && label.length"
            :class="[{ 'text-red-400': errorMessage.length }]"
            class="text-gray-500 text-md px-1 select-none"
            >{{ label }}</label
        >
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
            <input
                :class="[
                    {
                        'border-red-300  focus:ring-red-300 focus:border-red-400':
                            errorMessage.length,
                    },
                    $slots['append-icon'] && errorMessage.length
                        ? 'ltr:mr-20 rtl:pl-20'
                        : 'ltr:mr-10 rtl:pl-10',
                    { 'pl-10': prefix.length || $slots['prefix'] },
                ]"
                :disabled="customMode"
                :max="max"
                :min="min"
                :step="step"
                :value="modelValue"
                class="block w-full disabled:border-gray-300"
                type="range"
                v-bind="$attrs"
                @input="$emit('update:modelValue', $event.target.value)"
            />

            <div
                :class="[{ 'py-1.5': errorMessage.length }]"
                class="absolute inset-y-0 ltr:right-0 rtl:left-0 flex ltr:pr-1.5 rtl:pl-1.5 flex items-center gap-x-1"
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
        <template v-if="!hideProgressLabel">
            <div
                v-if="!customMode"
                class="px-3 cursor-pointer"
                @click.stop="enableCustomMode"
            >
                <slot :percentage="modelValue" name="percentage">
                    {{ modelValue }} %
                </slot>
            </div>
            <div v-else class="flex items-center w-fit">
                <text-field
                    v-model="customValue"
                    :min="0"
                    class="w-fit"
                    :max="100"
                    :step="step"
                    type="number"
                    @input="updateCustomValue"
                />
                <button
                    class="text-gray-400 inline-flex items-center"
                    @click="customMode = false"
                >
                    <material-icon :size="2">close</material-icon>
                </button>
            </div>
        </template>
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
import { ref, watch } from "vue";
import TextField from "@/Framework/TextField/TextField.vue";
import MaterialIcon from "@/Framework/Icon/MaterialIcon.vue";

defineOptions({
    name: "ProgressInput",
});

const props = defineProps({
    errorMessage: { type: String, default: "", required: false },
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
    password: { type: Boolean, default: false, required: false },
    hideProgressLabel: { type: Boolean, default: false, required: false },
    min: { type: Number, default: 0, required: false },
    max: { type: Number, default: 100, required: false },
    step: { type: Number, default: 1, required: false },
});

const emit = defineEmits(["update:modelValue"]);

const customMode = ref(false);
const customValue = ref(0);

function enableCustomMode() {
    customMode.value = true;
    customValue.value = props.modelValue;
}

function updateCustomValue(e) {
    let value = e.target.value;
    if (!(value > props.min)) {
        value = props.min;
    }
    if (value > props.max) {
        value = props.max;
    }

    emit("update:modelValue", value);
    // customMode.value = false
}

watch(customValue, (value) => {
    if (value > props.max) {
        customValue.value = props.max;
    }
    if (value < props.min) {
        customValue.value = props.min;
    }
});

function onClickPrepend() {
    props.onPrepend();
}

function onClickAppend() {
    props.onAppend();
}
</script>

<style scoped></style>
