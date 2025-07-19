<template>
    <Link
        :class="[
            {
                'font-semibold outline-hidden text-primary-500 rounded-md ring-primary-500 hover:text-primary-700':
                    !overrideStyles,
            },
        ]"
        :href="href"
        :method="method"
        as="button"
        type="button"
    >
        <slot />
    </Link>
</template>

<script setup>
import { Link } from "@inertiajs/vue3";

defineOptions({
    name: "NLink",
});

defineProps({
    method: { type: String, default: "get", required: false },
    href: { type: String, default: null, required: false },
    overrideStyles: { type: Boolean, default: false, required: false },
});
</script>

<style scoped></style>
