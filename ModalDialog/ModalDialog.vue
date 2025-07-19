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
            @close="
                !persistant
                    ? toggleDialog(false)
                    : $emit('onCloseAttempt', true)
            "
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

            <div class="fixed inset-0 overflow-y-auto overflow-x-hidden">
                <div
                    class="flex min-h-full items-center justify-center text-center"
                    :class="{ 'pt-4': !dense }"
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
                            class="max-w-8xl min-w-[100vw] transform overflow-hidden rounded-2xl bg-white text-left align-middle shadow-xl transition-all lg:min-w-[28rem]"
                            :class="[dense ? '' : 'p-6']"
                        >
                            <DialogTitle
                                as="h3"
                                class="text-lg leading-6 font-semibold text-gray-600"
                            >
                                <slot :open="toggleDialog" name="title" />
                            </DialogTitle>
                            <DialogDescription
                                class="text-gray-500 text-sm"
                                as="p"
                            >
                                <slot :open="toggleDialog" name="description" />
                            </DialogDescription>
                            <slot :open="toggleDialog" :isOpened="isOpen" />
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
    DialogDescription,
    DialogPanel,
    DialogTitle,
    TransitionChild,
    TransitionRoot,
} from "@headlessui/vue";

defineOptions({
    name: "ModalDialog",
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
    dense: {
        type: Boolean,
        default: false,
        required: false,
    },
});
const emit = defineEmits(["update:modelValue", "onClose", "onCloseAttempt"]);

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
