<template>
    <div class="flex flex-col transition-all duration-300">
        <div class="flex items-center gap-x-2">
            <label
                :class="[
                    { 'text-gray-300': disabled },
                    disabled ? 'cursor-auto' : 'cursor-default',
                    ,
                    labelClasses,
                ]"
                class="select-none"
                >{{ labelStart ? label : "" }}
                <input
                    class="mx-1"
                    :checked="modelValue ? trueValue : falseValue"
                    :class="[
                        errorMessage.length
                            ? 'text-red-400 ring-red-400 border-red-400 focus:ring-red-400 focus:border-red-400'
                            : 'text-primary-500 disabled:text-gray-200 disabled:ring-gray-200 disabled:border-gray-200',
                        ,
                        checkboxClasses,
                    ]"
                    :disabled="disabled || readOnly"
                    type="checkbox"
                    v-bind="$attrs"
                    @change="
                        (e) => {
                            let toVal = e.target.checked;
                            if (!toVal) {
                                toVal = falseValue;
                            } else {
                                toVal = trueValue;
                            }
                            $emit('update:modelValue', toVal);
                            $emit('change', toVal);
                        }
                    "
                />
                {{ !labelStart ? label : "" }}
            </label>
        </div>
        <div class="break-words text-gray-400 select-none">
            <p v-if="hint.length" class="text-sm px-1 mt-1">
                {{ hint }}
            </p>
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
defineOptions({
    name: "CheckBox",
});
defineProps({
    errorMessage: { type: String, default: "", required: false },
    checkboxClasses: { type: String, default: "", required: false },
    labelClasses: { type: String, default: "", required: false },
    modelValue: { type: Boolean, default: false, required: false },
    hint: { type: String, default: "", required: false },
    label: { type: String, default: "", required: false },
    disabled: { type: Boolean, default: false, required: false },
    readOnly: { type: Boolean, default: false, required: false },
    labelStart: { type: Boolean, default: false, required: false },
    hideDetails: { type: Boolean, default: false, required: false },
    trueValue: { default: true },
    falseValue: { default: false },
});
defineEmits(["update:modelValue", "change"]);
</script>

<style scoped></style>
