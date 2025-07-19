<template>
    <div
        class="flex h-fit w-screen left-0 items-center fixed bottom-0 flex-col z-50"
    >
        <div class="relative flex-1 w-fit">
            <div class="flex w-full lg:px-8">
                <div
                    class="flex w-full flex-col px-4 lg:px-8 rounded-t-lg drop-shadow-[0_-5px_15px_rgba(0,0,0,0.25)]"
                >
                    <slot :close="close" />
                </div>
            </div>
        </div>
    </div>
</template>

<script setup>
import { ref, watch } from "vue";

defineOptions({
    name: "SnackBar",
});

const props = defineProps({
    modelValue: {
        type: Boolean,
        default: false,
        required: false,
    },
});

const emit = defineEmits(["update:modelValue"]);

const snackbar = ref(props.modelValue);
watch(
    () => props.modelValue,
    (value) => {
        snackbar.value = value;
    },
);

function close() {
    snackbar.value = false;
    emit("update:modelValue", false);
}
</script>

<style scoped></style>
