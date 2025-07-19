<template>
    <slot :open="toggleDialog" name="activator" />
    <TransitionRoot
        :dir="$page.props.locale === 'en' ? 'ltr' : 'rtl'"
        :show="isOpen"
        appear
        as="template"
    >
        <Dialog
            as="div"
            class="relative z-30"
            @close="!persistant ? toggleDialog(false) : () => {}"
        >
            <TransitionChild
                as="template"
                enter="duration-300 ease-out"
                enter-from="opacity-0"
                enter-to="opacity-100"
                leave="duration-200 ease-in"
                leave-from="opacity-100"
                leave-to="opacity-0"
            >
                <div class="fixed inset-0 bg-black/25" />
            </TransitionChild>

            <div class="fixed inset-0 overflow-y-auto">
                <div
                    class="flex min-h-full items-center justify-center text-center"
                >
                    <TransitionChild
                        as="template"
                        enter="duration-300 ease-out"
                        enter-from="opacity-0 scale-95"
                        enter-to="opacity-100 scale-100"
                        leave="duration-200 ease-in"
                        leave-from="opacity-100 scale-100"
                        leave-to="opacity-0 scale-95"
                    >
                        <DialogPanel
                            class="w-screen transform overflow-hidden min-h-screen bg-white text-left align-middle shadow-xl transition-all"
                        >
                            <DialogTitle
                                v-if="$slots.title"
                                as="h3"
                                class="text-lg font-medium leading-6 text-gray-900 p-6"
                                :class="[
                                    flat
                                        ? 'border-b border-gray-100'
                                        : 'shadow-md',
                                ]"
                            >
                                <slot :open="toggleDialog" name="title" />
                            </DialogTitle>
                            <slot :open="toggleDialog" />
                        </DialogPanel>
                    </TransitionChild>
                </div>
            </div>
        </Dialog>
    </TransitionRoot>
</template>

<script setup>
import { ref, watch } from "vue";
import {
    Dialog,
    DialogPanel,
    DialogTitle,
    TransitionChild,
    TransitionRoot,
} from "@headlessui/vue";

defineOptions({
    name: "FullScreenModalDialog",
});

const props = defineProps({
    modelValue: {
        type: Boolean,
        default: false,
        required: false,
    },
    persistant: {
        type: Boolean,
        default: false,
        required: false,
    },
    flat: {
        type: Boolean,
        default: false,
        required: false,
    },
});

const emit = defineEmits(["update:modelValue", "onClose"]);

const isOpen = ref(props.modelValue);

watch(
    () => props.modelValue,
    (value) => {
        isOpen.value = value;
    },
);

function toggleDialog(value) {
    isOpen.value = value;
    emit("update:modelValue", value);
    if (!value) {
        emit("onClose");
    }
}
</script>

<style scoped></style>
