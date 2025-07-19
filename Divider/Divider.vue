<template>
    <div class="hidden sm:block">
        <div :class="[dense ? '' : vertical ? 'px-8' : 'py-8']">
            <div
                :class="[vertical ? 'border-l' : 'border-t']"
                class="border-gray-200"
            />
        </div>
    </div>
</template>

<script>
export default {
    name: "Divider",
    props: {
        vertical: {
            type: Boolean,
            default: false,
        },
        dense: {
            type: Boolean,
            default: false,
        },
    },
};
</script>

<style scoped></style>
