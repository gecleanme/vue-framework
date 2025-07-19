<template>
    <div
        v-if="breadcrumbs.length"
        class="flex items-center gap-x-2 font-bold text-gray-500 flex-wrap capitalize"
    >
        <template v-for="(breadcrumb, index) in breadcrumbs" :key="index">
            <span
                v-if="index !== 0"
                :class="[
                    breadcrumb.disabled ? 'text-gray-300' : 'text-primary-500',
                ]"
                class="font-bold mx-1"
                >{{ separator }}</span
            >

            <Link
                v-if="
                    (breadcrumb.to || breadcrumb.href) && !breadcrumb.disabled
                "
                :class="[
                    breadcrumb.disabled
                        ? 'text-gray-300'
                        : 'cursor-pointer text-primary-500 ',
                ]"
                :href="breadcrumb.to ? route(breadcrumb.to) : breadcrumb.href"
                class="font-bold text-sm lg:text-lg"
            >
                {{ breadcrumb.title }}
            </Link>
            <span
                v-else
                :class="[
                    breadcrumb.disabled
                        ? 'text-gray-300'
                        : 'cursor-pointer text-primary-500 ',
                ]"
                class="font-bold text-sm lg:text-lg"
                >{{ breadcrumb.title }}</span
            >
        </template>
    </div>
</template>

<script setup>
defineOptions({
    name: "Breadcrumbs",
});

defineProps({
    breadcrumbs: {
        type: Array,
        default: () => [],
        required: false,
    },
    separator: { type: String, default: "/", required: false },
});
</script>

<style scoped></style>
