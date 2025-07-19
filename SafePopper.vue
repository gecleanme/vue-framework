<script setup>
import { onBeforeUnmount, ref } from "vue";
import Popper from "vue3-popper";

const popperRef = ref(null);

onBeforeUnmount(() => {
    // Ensure proper cleanup before Vue tries to unmount
    if (popperRef.value && popperRef.value.popperInstance) {
        popperRef.value.popperInstance.destroy();
    }
});
</script>

<template>
    <Popper v-bind="$attrs" ref="popperRef">
        <slot></slot>
        <template #content>
            <slot name="content"></slot>
        </template>
    </Popper>
</template>
