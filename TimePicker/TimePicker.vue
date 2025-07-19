<template>
    <div class="flex flex-col w-fit justify-start">
        <label
            v-if="label && label.length"
            :class="[{ 'text-red-400': errorMessage.length }]"
            class="text-gray-500 text-md px-1 select-none"
            >{{ label }}</label
        >
        <div class="grid grid-cols-3 text-center">
            <div>
                <button
                    :disabled="hour === 23"
                    @click="hour += hourIncrement"
                    :dusk="duskHourUpButton"
                    class="text-primary-500 disabled:text-gray-200"
                >
                    <material-icon :size="6"> keyboard_arrow_up </material-icon>
                </button>
            </div>
            <div />
            <div>
                <button
                    :disabled="!(minute < 60 - minuteIncrement)"
                    @click="minute += minuteIncrement"
                    :dusk="duskMinuteUpButton"
                    class="text-primary-500 disabled:text-gray-200"
                >
                    <material-icon :size="6"> keyboard_arrow_up </material-icon>
                </button>
            </div>
            <div>
                <span class="text-2xl text-gray-700">{{ hour }}</span>
            </div>
            <div>
                <span class="text-2xl mx-2">:</span>
            </div>
            <div>
                <span class="text-2xl text-gray-700">{{ minute }}</span>
            </div>
            <div>
                <button
                    :disabled="!(hour > 0)"
                    @click="hour -= hourIncrement"
                    :dusk="duskHourDownButton"
                    class="text-primary-500 disabled:text-gray-200"
                >
                    <material-icon :size="6">
                        keyboard_arrow_down
                    </material-icon>
                </button>
            </div>
            <div />
            <div>
                <button
                    :disabled="!(minute > 0)"
                    @click="minute -= minuteIncrement"
                    :dusk="duskMinuteDownButton"
                    class="text-primary-500 disabled:text-gray-200"
                >
                    <material-icon :size="6">
                        keyboard_arrow_down
                    </material-icon>
                </button>
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
import MaterialIcon from "@/Framework/Icon/MaterialIcon.vue";
import { computed, onMounted, ref, watch } from "vue";

defineOptions({
    name: "TimePicker",
});

const props = defineProps({
    hourIncrement: {
        type: Number,
        default: 1,
    },
    minuteIncrement: {
        type: Number,
        default: 15,
    },
    modelValue: {
        type: String,
    },
    errorMessage: { type: String, default: "", required: false },
    label: { type: String, default: null, required: false },
    duskHourUpButton: { type: String, default: null, required: false },
    duskHourDownButton: { type: String, default: null, required: false },
    duskMinuteUpButton: { type: String, default: null, required: false },
    duskMinuteDownButton: { type: String, default: null, required: false },
});

const emit = defineEmits(["update:modelValue"]);
const hour = ref(0);
const minute = ref(0);

const formatedHour = computed(() => {
    if (hour.value < 10) {
        return "0" + hour.value;
    } else {
        return hour.value;
    }
});

const formatedMinute = computed(() => {
    if (minute.value < 10) {
        return "0" + minute.value;
    } else {
        return minute.value;
    }
});

onMounted(() => {
    const valueArray = props.modelValue.split(":");
    hour.value =
        Math.round(Number(valueArray[0]) / props.hourIncrement) *
        props.hourIncrement;
    minute.value =
        Math.round(Number(valueArray[1]) / props.minuteIncrement) *
        props.minuteIncrement;
});

watch(hour, () => {
    emit("update:modelValue", formatedHour.value + ":" + formatedMinute.value);
});
watch(minute, () => {
    emit("update:modelValue", formatedHour.value + ":" + formatedMinute.value);
});

watch(
    () => props.modelValue,
    () => {
        const valueArray = props.modelValue.split(":");
        hour.value =
            Math.round(Number(valueArray[0]) / props.hourIncrement) *
            props.hourIncrement;
        minute.value =
            Math.round(Number(valueArray[1]) / props.minuteIncrement) *
            props.minuteIncrement;
    },
);
</script>
