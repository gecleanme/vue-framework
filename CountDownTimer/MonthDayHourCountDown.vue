<template>
    <div class="flex items-center justify-center px-5 py-5">
        <div :class="[textColor, hide ? 'opacity-0' : 'opacity-100']">
            <div
                class="text-3xl text-center flex w-full items-center justify-center"
            >
                <div
                    v-if="formattedTime.months > 0"
                    class="mx-1 p-2 bg-white rounded-lg"
                >
                    <div class="font-mono leading-none">
                        {{ formattedTime.months }}
                    </div>
                    <div class="font-mono uppercase text-sm leading-none">
                        Months
                    </div>
                </div>
                <div
                    v-if="formattedTime.days > 0"
                    class="mx-1 p-2 bg-white rounded-lg"
                >
                    <div class="font-mono leading-none">
                        {{ formattedTime.days }}
                    </div>
                    <div class="font-mono uppercase text-sm leading-none">
                        Days
                    </div>
                </div>
                <div
                    v-if="formattedTime.hours > 0"
                    class="mx-1 p-2 bg-white rounded-lg"
                >
                    <div class="font-mono leading-none">
                        {{ formattedTime.hours }}
                    </div>
                    <div class="font-mono uppercase text-sm leading-none">
                        Hours
                    </div>
                </div>
                <div class="mx-1 p-2 bg-white rounded-lg">
                    <div class="font-mono leading-none">
                        {{ formattedTime.minutes }}
                    </div>
                    <div class="font-mono uppercase text-sm leading-none">
                        Minutes
                    </div>
                </div>
                <div class="mx-1 p-2 bg-white rounded-lg">
                    <div class="font-mono leading-none">
                        {{ formattedTime.seconds }}
                    </div>
                    <div class="font-mono uppercase text-sm leading-none">
                        Seconds
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup>
import { computed, onMounted, ref, watch } from "vue";

defineOptions({
    name: "MonthDayHourCountDown",
});
const props = defineProps({
    end: {
        type: Date,
        default: () => new Date(),
    },
    hide: { type: Boolean, default: false, required: false },
});
const emit = defineEmits(["update:modelValue"]);
const remaining = ref(0);
onMounted(() => {
    const now = new Date(
        new Date().toLocaleString("en-US", { timeZone: "Asia/Riyadh" }),
    );
    remaining.value = Math.round((props.end - now) / 1000);
    emit("update:modelValue", remaining.value);
});

const formattedTime = computed(() => {
    let remaining_seconds = remaining.value;
    const months = Math.max(Math.floor(remaining_seconds / 2628000), 0);
    remaining_seconds -= months * 2628000;
    const days = Math.max(Math.floor(remaining_seconds / 86400), 0);
    remaining_seconds -= days * 86400;
    const hours = Math.max(Math.floor(remaining_seconds / 3600), 0);
    remaining_seconds -= hours * 3600;
    const minutes = Math.max(Math.floor(remaining_seconds / 60), 0);
    remaining_seconds -= minutes * 60;
    const seconds = Math.max(remaining_seconds, 0);
    return { months, days, hours, minutes, seconds };
});

const textColor = computed(() => {
    if (remaining.value < 10 * 60 * 60) {
        return "text-red-500";
    } else if (remaining.value < 2 * 60 * 60) {
        return "text-yellow-500";
    } else {
        return "text-primary-500";
    }
});

watch(
    remaining,
    () => {
        setTimeout(() => {
            const now = new Date(
                new Date().toLocaleString("en-US", {
                    timeZone: "Asia/Riyadh",
                }),
            );
            remaining.value = Math.round((props.end - now) / 1000);
            emit("update:modelValue", remaining.value);
        }, 1000);
    },
    {
        immediate: true,
    },
);
</script>

<style scoped></style>
