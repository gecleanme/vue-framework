<template>
    <button
        class="text-gray-500 mx-2 py-2 px-3 text-xs capitalize ring-1 ring-primary-500 rounded-md"
        @click="$inertia.visit(route('switch.locale'))"
    >
        {{ $page.props.locale === "en" ? "عربي" : "English" }}
    </button>
</template>

<script setup>
defineOptions({
    name: "LangButton",
});
</script>

<style scoped></style>
