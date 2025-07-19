<template>
    <div class="flex gap-4">
        <label
            v-if="label && label.length"
            :class="[
                {
                    'text-red-400':
                        errorMessageFrom.length || errorMessageTo.length,
                },
            ]"
            class="text-md select-none px-1 text-gray-500"
            >{{ label }}</label
        >
    </div>
    <div class="mt-1! flex items-center gap-4">
        <div class="flex items-center justify-center gap-4">
            <div class="flex flex-col-reverse items-start gap-1">
                <p
                    v-if="errorMessageFrom.length"
                    class="flex flex-col px-1 text-sm text-red-400"
                >
                    <span
                        id="error-message-from"
                        v-if="errorMessageFrom.length"
                        >{{ errorMessageFrom }}</span
                    >
                </p>
                <number-field
                    :class="[
                        {
                            'border-red-400':
                                (errorMessageFrom && errorMessageFrom.length) ||
                                (errorMessageTo && errorMessageTo.length),
                        },
                        { 'text-field-container--disabled': disabled },
                        disabled ? 'bg-gray-50' : 'bg-white',
                    ]"
                    class="reset--text-field block w-[4.5rem] text-center placeholder:text-gray-400 xl:w-[7rem]"
                    v-model="from"
                    :min-value="1"
                    :placeholder="useTranslate('asset_scheduling', 'from')"
                />
                <span class="ml-1 text-gray-600">{{
                    useTranslate("asset_scheduling", "from")
                }}</span>
            </div>
            <span class="mt-5">:</span>
        </div>
        <div class="flex flex-col-reverse items-start gap-1">
            <p
                v-if="errorMessageTo.length"
                class="flex flex-col px-1 text-sm text-red-400"
            >
                <span id="error-message-to" v-if="errorMessageTo.length">{{
                    errorMessageTo
                }}</span>
            </p>
            <number-field
                :class="[
                    {
                        'border-red-400':
                            (errorMessageFrom && errorMessageFrom.length) ||
                            (errorMessageTo && errorMessageTo.length),
                    },
                    { 'text-field-container--disabled': disabled },
                    disabled ? 'bg-gray-50' : 'bg-white',
                ]"
                class="reset--text-field block w-[4.5rem] text-center placeholder:text-gray-400 xl:w-[7rem]"
                v-model="to"
                :min-value="1"
                :placeholder="useTranslate('asset_scheduling', 'to')"
            />
            <span class="ml-1 text-gray-600">{{
                useTranslate("asset_scheduling", "to")
            }}</span>
        </div>
    </div>
</template>

<script setup>
import { useTranslate } from "@/Composables/Translate";
import NumberField from "@/Framework/NumberField/NumberField.vue";

defineProps({
    errorMessageFrom: { type: String, default: "", required: false },
    errorMessageTo: { type: String, default: "", required: false },
    modelValue: { type: Number, required: false },
    label: { type: String },
    disabled: { type: Boolean, default: false },
});

const from = defineModel("from");
const to = defineModel("to");
</script>
