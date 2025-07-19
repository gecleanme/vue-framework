<template>
    <div class="flex">
        <StarIcon
            class="w-12 text-gray-200"
            v-for="i in 5"
            :key="i"
            :class="{ 'text-yellow-400': i <= rate }"
        />
    </div>
</template>

<script setup>
import { StarIcon } from "@heroicons/vue/solid";

defineOptions({
    name: "StarRateDisplay",
});

defineProps({
    rate: {
        type: Number,
        default: 0,
    },
});
</script>

<style scoped></style>
