<template>
    <a v-if="email" :href="'mailto:' + email">
        <slot>
            <span class="text-primary-500 outline-primary-400 cursor-pointer">
                {{ email }}
            </span>
        </slot>
    </a>
</template>

<script setup>
defineOptions({
    name: "ClickToEmailLabel",
});

defineProps({
    email: { type: String, default: null, required: false },
});
</script>

<style scoped></style>
