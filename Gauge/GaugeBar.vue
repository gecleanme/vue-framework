<script setup>
import { computed } from "vue";

defineOptions({
    name: "AudioLevelGaugeBar",
});

const props = defineProps({
    progress: {
        type: Number,
        default: 0,
        validator: (value) => value >= 0 && value <= 1,
    },
    vertical: {
        type: Boolean,
        default: false,
    },
});

const normalizedProgress = computed(() => Math.round(props.progress * 10));

const getBarColor = (index) => {
    if (index > normalizedProgress.value) return "bg-gray-300";

    if (index <= 3) return "bg-green-500";
    if (index <= 6) return "bg-yellow-500";
    if (index <= 8) return "bg-orange-500";
    return "bg-red-500";
};
</script>

<template>
    <div class="flex items-center gap-x-4 text-gray-500 scale-75 lg:scale-100">
        <div class="flex gap-0.5" :class="{ 'flex-col-reverse': vertical }">
            <div
                v-for="i in 10"
                :key="`bar-${i}`"
                :class="['w-12', 'h-2', getBarColor(i)]"
            />
        </div>
    </div>
</template>

<style scoped></style>
