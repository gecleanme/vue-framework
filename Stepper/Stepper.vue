<template>
    <div class="w-full">
        <div class="flex items-center justify-between p-4">
            <div
                v-for="(step, index) in steps"
                :key="`${step.key}`"
                class="flex items-center flex-1 last:grow-0"
            >
                <div
                    class="flex items-center whitespace-nowrap select-none cursor-pointer"
                    @click="() => handleStepClick(index)"
                >
                    <pop-over-tip position="bottom">
                        <template #default>
                            <div
                                class="flex flex-col bg-white p-3 shadow-md rounded-md"
                                v-if="Object.keys(errorsGrouped[index]).length"
                            >
                                <span
                                    v-for="field in Object.keys(
                                        errorsGrouped[index],
                                    )"
                                    :key="`stepper-error${field}`"
                                    class="text-red-500 text-xs"
                                >
                                    {{ errorsGrouped[index][field] }}
                                </span>
                            </div>
                        </template>
                        <template #content>
                            <div class="flex flex-col items-center gap-1">
                                <div
                                    class="w-7 h-7 rounded-full flex items-center justify-center ltr:mr-2 rtl:ml-2"
                                    :class="[
                                        getStepClass(index),
                                        Object.keys(errorsGrouped[index])
                                            .length > 0
                                            ? ''
                                            : '',
                                    ]"
                                    :key="currentStep"
                                >
                                    <slot
                                        name="step-icon"
                                        :step="step"
                                        :index="index"
                                        :isComplete="isStepComplete(index)"
                                        :errors="errorsGrouped[index]"
                                    >
                                        <span
                                            v-if="!isStepComplete(index)"
                                            class="text-sm font-semibold"
                                            >{{ index + 1 }}</span
                                        >
                                        <span
                                            v-else
                                            class="leading-none text-xl"
                                        >
                                            <material-icon :size="2">{{
                                                Object.keys(
                                                    errorsGrouped[index],
                                                ).length > 0
                                                    ? "warning"
                                                    : "done"
                                            }}</material-icon>
                                        </span>
                                    </slot>
                                </div>
                                <slot
                                    name="step-label"
                                    :step="step"
                                    :index="index"
                                >
                                    <span
                                        :class="[
                                            getTextClass(index),
                                            ' md:hidden text-xs',
                                        ]"
                                        >{{ step.label }}</span
                                    >
                                </slot>
                            </div>
                        </template>
                    </pop-over-tip>

                    <slot name="step-label" :step="step" :index="index">
                        <span
                            :class="[
                                getTextClass(index),
                                'hidden md:inline-block',
                            ]"
                            >{{ step.label }}</span
                        >
                    </slot>
                </div>
                <div
                    v-if="index < steps.length - 1"
                    class="grow mx-2 min-w-[20px]"
                >
                    <slot name="step-connector" :index="index">
                        <div class="h-0.5 bg-gray-200">
                            <div
                                class="h-full transition-all duration-300"
                                :style="{ width: getLineWidth(index) }"
                                :class="
                                    Object.keys(errorsGrouped[index] ?? {})
                                        ?.length > 0
                                        ? 'bg-red-400'
                                        : 'bg-green-500'
                                "
                            ></div>
                        </div>
                    </slot>
                </div>
            </div>
        </div>

        <div class="my-4">
            <template v-for="step in steps" :key="`step-contents-${step.key}`">
                <div class="" v-show="currentStepKey === step.key">
                    <slot :name="`item.${step.key}`" :item="step">
                        <!-- Default content if no slot is provided -->
                        <p>Content for {{ step.label }}</p>
                    </slot>
                </div>
            </template>
            <div class="py-3 flex items-center justify-between">
                <primary-button
                    v-if="currentStep > 1"
                    @click="handlePrevious"
                    color="gray"
                    :disabled="isPreviousDisabled"
                >
                    <slot name="previous-button-text">
                        {{ useTranslate("commons", "previous") }}
                    </slot>
                </primary-button>
                <div v-else></div>

                <div class="flex gap-3 items-center">
                    <slot
                        name="prepend-next-actions"
                        :currentStep="currentStep"
                        :stepsCount="steps.length"
                    >
                    </slot>
                    <primary-button
                        v-if="currentStep < steps.length"
                        @click="handleNext"
                        :disabled="isNextDisabled"
                    >
                        <slot name="next-button-text">
                            {{ useTranslate("commons", "next") }}
                        </slot>
                    </primary-button>
                    <slot name="submit-actions" v-else> </slot>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup>
import { computed } from "vue";
import { useTranslate } from "@/Composables/Translate.js";
import PrimaryButton from "@/Framework/Button/PrimaryButton.vue";
import PopOverTip from "@/Framework/ToolTip/PopOverTip.vue";
import MaterialIcon from "@/Framework/Icon/MaterialIcon.vue";

const props = defineProps({
    steps: {
        type: Array,
        required: true,
    },
    currentStep: {
        type: Number,
        required: true,
    },
    editMode: {
        type: Boolean,
        default: false,
    },
    stepDisabledConditions: {
        type: Object,
        default: () => ({}),
    },
    errorMessages: {
        type: Object,
        default: () => ({}),
    },
});

const emit = defineEmits(["update:currentStep"]);

const currentStepKey = computed(() => props.steps[props.currentStep - 1].key);
const errorsGrouped = computed(() => {
    const errors = props.errorMessages ?? {};

    return props.steps.map((s) => {
        const error_fields = s.error_fields ?? [];
        return Object.keys(errors)
            .filter((field) => error_fields.includes(field))
            .reduce((acc, field) => {
                acc[field] = errors[field];
                return acc;
            }, {});
    });
});

const isNextDisabled = computed(() => {
    const conditions = props.stepDisabledConditions[currentStepKey.value] || {};
    return conditions.next || false;
});

const isPreviousDisabled = computed(() => {
    const conditions = props.stepDisabledConditions[currentStepKey.value] || {};
    return conditions.previous || false;
});

const isStepComplete = (index) => {
    return props.editMode || props.currentStep > index + 1;
};

const getStepClass = (index) => {
    let common_types = "";
    if (props.currentStep === index + 1) {
        common_types = "ring-2 ring-offset-2 ";
    }
    if (Object.keys(errorsGrouped.value[index]).length > 0) {
        return common_types + "bg-red-400 text-white ring-red-400/60";
    }
    if (isStepComplete(index)) {
        return common_types + "bg-green-500 text-white ring-green-400/60";
    } else if (props.currentStep === index + 1) {
        return common_types + "bg-green-500 text-white ring-green-400/60";
    } else {
        return common_types + "bg-gray-200 text-gray-500 ring-gray-400/60";
    }
};

const getTextClass = (index) => {
    if (Object.keys(errorsGrouped.value[index]).length > 0) {
        return "text-red-500 font-semibold";
    }
    if (isStepComplete(index)) {
        return "text-green-500 font-semibold";
    } else if (props.currentStep === index + 1) {
        return "text-green-500 font-semibold";
    } else {
        return "text-gray-500";
    }
};

const getLineWidth = (index) => {
    if (props.editMode || props.currentStep > index + 1) {
        return "100%";
    }
    return "0%";
};

const handleStepClick = (index) => {
    if (props.editMode) {
        emit("update:currentStep", index + 1);
    }
};

const handlePrevious = () => {
    if (props.currentStep > 1 && !isPreviousDisabled.value) {
        emit("update:currentStep", props.currentStep - 1);
    }
};

const handleNext = () => {
    if (props.currentStep < props.steps.length && !isNextDisabled.value) {
        emit("update:currentStep", props.currentStep + 1);
    }
};
</script>
