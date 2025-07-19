<template>
    <div class="flex w-full justify-start">
        <label
            v-if="label && label.length"
            :class="[{ 'text-red-400': errorMessage.length }]"
            class="text-md select-none px-1 text-gray-500"
            >{{ label }}</label
        >
    </div>
    <div class="mt-1! flex items-center gap-4">
        <div class="flex items-center gap-4" v-if="showHours">
            <div class="flex flex-col-reverse items-start gap-1">
                <number-field
                    :class="[
                        {
                            'border-red-400':
                                errorMessage && errorMessage.length,
                        },
                        { 'text-field-container--disabled': disabled },
                        disabled ? 'bg-gray-50' : 'bg-white',
                    ]"
                    class="reset--text-field block w-[4.5rem] text-center placeholder:text-gray-400 xl:w-[7rem]"
                    v-model="hours"
                    :min-value="0"
                    :max-value="23"
                    :placeholder="useTranslate('commons', 'hours')"
                />
                <span class="ml-1 text-gray-600">{{
                    useTranslate("commons", "hours")
                }}</span>
            </div>
            <span class="mt-5">:</span>
        </div>
        <div class="flex items-center justify-center gap-4" v-if="showMinutes">
            <div class="flex flex-col-reverse items-start gap-1">
                <number-field
                    :class="[
                        {
                            'border-red-400':
                                errorMessage && errorMessage.length,
                        },
                        { 'text-field-container--disabled': disabled },
                        disabled ? 'bg-gray-50' : 'bg-white',
                    ]"
                    class="reset--text-field block w-[4.5rem] text-center placeholder:text-gray-400 xl:w-[7rem]"
                    v-model="minutes"
                    :min-value="0"
                    :max-value="59"
                    :placeholder="useTranslate('commons', 'minutes')"
                />
                <span class="ml-1 text-gray-600">{{
                    useTranslate("commons", "minutes")
                }}</span>
            </div>
            <span class="mt-5">:</span>
        </div>
        <div class="flex flex-col-reverse items-start gap-1">
            <number-field
                :class="[
                    { 'border-red-400': errorMessage && errorMessage.length },
                    { 'text-field-container--disabled': disabled },
                    disabled ? 'bg-gray-50' : 'bg-white',
                ]"
                class="reset--text-field block w-[4.5rem] text-center placeholder:text-gray-400 xl:w-[7rem]"
                v-model="seconds"
                :min-value="0"
                :max-value="59"
                :placeholder="useTranslate('commons', 'seconds')"
            />
            <span class="ml-1 text-gray-600">{{
                useTranslate("commons", "seconds")
            }}</span>
        </div>
    </div>
    <p v-if="errorMessage.length" class="px-1 text-sm text-red-400">
        {{ errorMessage }}
    </p>
</template>

<script setup>
import { computed } from "vue";
import { useTranslate } from "@/Composables/Translate";
import NumberField from "@/Framework/NumberField/NumberField.vue";

const props = defineProps({
    errorMessage: { type: String, default: "", required: false },
    modelValue: { type: Number, required: true },
    showMinutes: { type: Boolean, default: true },
    showHours: { type: Boolean, default: true },
    showSeconds: { type: Boolean, default: true },
    label: { type: String },
    disabled: { type: Boolean, default: false },
});

const emit = defineEmits(["update:modelValue"]);

const hours = computed({
    get: () => Math.floor(props.modelValue / 3600),
    set: (newVal) => updateModelValue(newVal, minutes.value, seconds.value),
});

const minutes = computed({
    get: () => Math.floor((props.modelValue % 3600) / 60),
    set: (newVal) => updateModelValue(hours.value, newVal, seconds.value),
});

const seconds = computed({
    get: () => props.modelValue % 60,
    set: (newVal) => updateModelValue(hours.value, minutes.value, newVal),
});

const updateModelValue = (h, m, s) => {
    const totalSeconds = (h || 0) * 3600 + (m || 0) * 60 + (s || 0);
    emit("update:modelValue", totalSeconds);
};
</script>
