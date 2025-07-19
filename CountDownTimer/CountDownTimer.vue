<template>
    <slot
        :hours="formattedTime.hours"
        :minutes="formattedTime.minutes"
        :seconds="formattedTime.seconds"
    >
        <div
            :class="timeStyle.class"
            class="text-lg font-bold select-none rounded-lg p-3"
            :style="timeStyle.styles"
        >
            {{ title }} {{ formattedTime.hours }}:{{ formattedTime.minutes }}:{{
                formattedTime.seconds
            }}
        </div>
    </slot>
</template>

<script setup>
import { computed, onMounted, onUnmounted, ref } from "vue";
import {
    useTodayInstance,
    useToSystemTimeZoneInstance,
} from "@/Composables/toUserTimezone.js";
import firstReminderSound from "@/../audio/copper-bell-ding-3.mp3";
import secondReminderSound from "@/../audio/copper-bell-ding-8.mp3";

defineOptions({
    name: "CountDownTimer",
});

const props = defineProps({
    end: {
        type: String,
        default: null,
    },
    playSounds: {
        type: Boolean,
        default: false,
    },
    title: {
        type: String,
        default: "the remaining time",
    },
    modelValue: {
        default: 0,
    },
    firstReminderData: {
        type: Object,
        default: () => ({
            seconds: 10 * 60,
            background_color: "#eab308",
            text_color: "#fefce8",
        }),
        required: true,
    },
    secondReminderData: {
        type: Object,
        default: () => ({
            seconds: 2 * 60,
            background_color: "#ef4444",
            text_color: "#fef2f2",
        }),
        required: true,
    },
});

const emit = defineEmits(["update:modelValue"]);
let callAudio = null;
const remaining = ref(0);
const firstReminderNotified = ref(false);
const secondReminderNotified = ref(false);
const isUserInteracted = ref(false);

function playAudio(audioPath) {
    callAudio = new Audio(audioPath);
    callAudio.volume = 0.5;
    callAudio.play();
}

const playFirstReminder = () => {
    if (firstReminderNotified.value) {
        return;
    }
    playAudio(firstReminderSound);
    firstReminderNotified.value = true;
};
const playSecondReminder = () => {
    if (secondReminderNotified.value) {
        return;
    }
    playAudio(secondReminderSound);
    secondReminderNotified.value = true;
};

const playReminderSound = () => {
    if (!isUserInteracted.value || !props.playSounds) {
        return;
    }
    if (firstReminderNotified.value && secondReminderNotified.value) {
        return;
    }
    if (remaining.value <= props.secondReminderData.seconds) {
        playSecondReminder();
        return;
    }

    if (remaining.value <= props.firstReminderData.seconds) {
        playFirstReminder();
    }
};
const calculateRemaining = () => {
    const now = useTodayInstance();
    const end = props.end
        ? useToSystemTimeZoneInstance(props.end)
        : now.clone();
    remaining.value = end.diff(now, "seconds");

    playReminderSound();
};

const displayRemaining = computed(() => Math.max(remaining.value, 0));

const timeStyle = computed(() => {
    if (remaining.value <= props.secondReminderData.seconds) {
        return {
            class: "rounded-lg bg-red-500 text-red-50 p-3 animate-wiggle",
            styles: {
                color: props.secondReminderData.text_color,
                backgroundColor: props.secondReminderData.background_color,
            },
        };
    }
    if (remaining.value <= props.firstReminderData.seconds) {
        return {
            class: "",
            styles: {
                color: props.firstReminderData.text_color,
                backgroundColor: props.firstReminderData.background_color,
            },
        };
    }

    return {
        class: "bg-primary-500 text-white",
        styles: {},
    };
});

const formattedTime = computed(() => {
    const hours = Math.trunc(displayRemaining.value / 60 / 60);
    const minutes = String(
        Math.trunc(displayRemaining.value / 60) - hours * 60,
    ).padStart(2, "0");
    const seconds = String(
        displayRemaining.value - hours * 60 * 60 - minutes * 60,
    ).padStart(2, "0");
    return {
        hours,
        minutes,
        seconds,
    };
});

const updateDisplay = () => {
    calculateRemaining();
    emit("update:modelValue", remaining.value);
};

let intervalId;

const handleUserInteraction = () => {
    isUserInteracted.value = true;
};

const initializeMedia = () => {
    document.addEventListener("click", handleUserInteraction, { once: true });
    document.addEventListener("keydown", handleUserInteraction, { once: true });
};

onMounted(() => {
    initializeMedia();

    calculateRemaining();
    emit("update:modelValue", remaining.value);

    intervalId = setInterval(updateDisplay, 1000);
});

onUnmounted(() => {
    clearInterval(intervalId);
    document.removeEventListener("click", handleUserInteraction);
    document.removeEventListener("keydown", handleUserInteraction);
});
</script>

<style lang="scss" scoped></style>
