<template>
    <slot name="activator" />
    <TransitionRoot as="template" :show="open">
        <Dialog
            as="div"
            class="fixed inset-0 overflow-hidden z-50"
            @close="closePanel"
        >
            <div class="absolute inset-0 overflow-hidden">
                <TransitionChild
                    as="template"
                    enter="ease-in-out duration-500"
                    enter-from="opacity-0"
                    enter-to="opacity-100"
                    leave="ease-in-out duration-500"
                    leave-from="opacity-100"
                    leave-to="opacity-0"
                >
                    <DialogOverlay
                        class="absolute inset-0 bg-gray-500/75 transition-opacity"
                    />
                </TransitionChild>

                <div
                    class="pointer-events-none fixed inset-y-0 right-0 flex max-w-full pl-10 sm:pl-16"
                >
                    <TransitionChild
                        as="template"
                        enter="transform transition ease-in-out duration-500 sm:duration-700"
                        enter-from="translate-x-full"
                        enter-to="translate-x-0"
                        leave="transform transition ease-in-out duration-500 sm:duration-700"
                        leave-from="translate-x-0"
                        leave-to="translate-x-full"
                    >
                        <div class="pointer-events-auto w-screen max-w-md">
                            <form
                                class="flex h-full flex-col divide-y divide-gray-200 bg-white shadow-xl"
                            >
                                <div class="h-0 flex-1 overflow-y-auto">
                                    <div
                                        class="bg-primary-700 py-6 px-4 sm:px-6"
                                        v-if="$slots['title']"
                                    >
                                        <div
                                            class="flex items-center justify-between"
                                        >
                                            <DialogTitle
                                                class="text-lg font-medium text-white"
                                            >
                                                <slot name="title" />
                                            </DialogTitle>
                                            <div
                                                class="ltr:ml-3 rtl:mr-3 flex h-7 items-center"
                                            >
                                                <button
                                                    type="button"
                                                    class="rounded-md bg-primary-700 text-primary-200 hover:text-white focus:outline-hidden focus:text-white"
                                                    @click="closePanel"
                                                >
                                                    <span class="sr-only"
                                                        >Close panel</span
                                                    >
                                                    <XMarkIcon
                                                        class="h-6 w-6"
                                                        aria-hidden="true"
                                                    />
                                                </button>
                                            </div>
                                        </div>
                                        <div class="mt-1">
                                            <p class="text-sm text-primary-300">
                                                <slot name="description" />
                                            </p>
                                        </div>
                                    </div>
                                    <div
                                        class="flex flex-1 flex-col justify-between"
                                    >
                                        <slot />
                                    </div>
                                </div>
                                <div
                                    class="flex shrink-0 justify-end px-4 py-4 gap-2"
                                    v-if="$slots['actions']"
                                >
                                    <slot name="actions" />
                                </div>
                            </form>
                        </div>
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
    DialogOverlay,
    DialogTitle,
    TransitionChild,
    TransitionRoot,
} from "@headlessui/vue";
import { XMarkIcon } from "@heroicons/vue/24/outline";
defineOptions({
    name: "SidePanel",
});

const props = defineProps({
    modelValue: {
        type: Boolean,
        default: false,
    },
});

const emit = defineEmits(["update:modelValue"]);

const open = ref(false);

function closePanel() {
    open.value = false;
    emit("update:modelValue", false);
}
watch(
    () => props.modelValue,
    (value) => {
        open.value = value;
    },
);

defineExpose({ open });
</script>

<style scoped></style>
