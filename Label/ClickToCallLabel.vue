<template>
    <a v-if="phone" :href="'tel:' + phone">
        <slot>
            <span class="text-primary-500 outline-primary-400 cursor-pointer">
                {{ phone }}
            </span>
        </slot>
    </a>
</template>

<script setup>
defineOptions({
    name: "ClickToCallLabel",
});

defineProps({
    phone: { type: String, default: null, required: false },
});
</script>

<style scoped></style>
