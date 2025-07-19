<script setup>
import { ref, watch } from "vue";

const props = defineProps({
    isCollapsed: {
        type: Boolean,
        default: false,
    },
});

const collapsed = ref(props.isCollapsed);

const toggleCollapsed = (collaps = null) => {
    if (collaps !== null) {
        collapsed.value = collaps;
        return;
    }
    collapsed.value = !collapsed.value;
};

watch(
    () => props.isCollapsed,
    () => {
        collapsed.value = props.isCollapsed;
    },
);
</script>

<template>
    <div class="flex flex-col">
        <slot :collapsed="collapsed" :toggleCollapsed="toggleCollapsed" />
        <div class="" v-if="collapsed">
            <slot
                name="content"
                :collapsed="collapsed"
                :toggleCollapsed="toggleCollapsed"
            />
        </div>
    </div>
</template>

<style scoped></style>
