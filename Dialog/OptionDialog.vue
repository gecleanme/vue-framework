<template>
    <TransitionRoot
        :dir="$page.props.locale === 'en' ? 'ltr' : 'rtl'"
        :show="dialog"
        as="template"
    >
        <Dialog
            as="div"
            class="fixed z-30 inset-0 overflow-y-auto"
            @close="cancel"
        >
            <div
                class="flex items-end justify-center min-h-screen pt-4 px-4 pb-20 text-center sm:block sm:p-0"
            >
                <TransitionChild
                    as="template"
                    enter="ease-out duration-300"
                    enter-from="opacity-0"
                    enter-to="opacity-100"
                    leave="ease-in duration-200"
                    leave-from="opacity-100"
                    leave-to="opacity-0"
                >
                    <DialogOverlay
                        class="fixed inset-0 bg-gray-500/75 transition-opacity"
                    />
                </TransitionChild>
                <span
                    aria-hidden="true"
                    class="hidden sm:inline-block sm:align-middle sm:h-screen"
                    >&#8203;</span
                >
                <TransitionChild
                    as="template"
                    enter="ease-out duration-300"
                    enter-from="opacity-0 translate-y-4 sm:translate-y-0 sm:scale-95"
                    enter-to="opacity-100 translate-y-0 sm:scale-100"
                    leave="ease-in duration-200"
                    leave-from="opacity-100 translate-y-0 sm:scale-100"
                    leave-to="opacity-0 translate-y-4 sm:translate-y-0 sm:scale-95"
                >
                    <div
                        class="relative inline-block align-bottom bg-white rounded-lg px-4 pt-5 pb-4 text-left overflow-hidden shadow-xl transform transition-all sm:my-8 sm:align-middle sm:max-w-lg sm:w-full sm:p-6"
                    >
                        <div class="sm:flex sm:items-start">
                            <slot name="icon">
                                <div
                                    class="mx-auto shrink-0 flex items-center justify-center h-12 w-12 rounded-full bg-red-100 sm:mx-0 sm:h-10 sm:w-10"
                                >
                                    <ExclamationIcon
                                        aria-hidden="true"
                                        class="h-6 w-6 text-red-600"
                                    />
                                </div>
                            </slot>
                            <div
                                class="mt-3 text-center sm:mt-0 sm:ltr:ml-4 sm:rtl:mr-4 sm:text-left"
                            >
                                <DialogTitle
                                    as="h3"
                                    class="text-lg leading-6 font-medium text-gray-900"
                                >
                                    {{ title }}
                                </DialogTitle>
                                <div class="mt-2">
                                    <p class="text-sm text-gray-500">
                                        {{ message }}
                                    </p>
                                </div>
                            </div>
                        </div>
                        <div
                            class="mt-5 sm:mt-4 flex flex-col lg:flex-row lg:flex-row-reverse justify-start lg:gap-2"
                        >
                            <div
                                class="flex flex-wrap w-full justify-center gap-2"
                            >
                                <primary-button
                                    @click="agree(option.value)"
                                    class="w-full lg:min-w-[25%] lg:w-fit"
                                    v-for="option in options"
                                    :key="option"
                                >
                                    <div class="flex gap-1 items-center">
                                        <material-icon v-if="option.icon">
                                            {{ option.icon }}
                                        </material-icon>
                                        <span>
                                            {{ option.text }}
                                        </span>
                                    </div>
                                </primary-button>
                            </div>
                        </div>
                    </div>
                </TransitionChild>
            </div>
        </Dialog>
    </TransitionRoot>
</template>

<script setup>
import {
    Dialog,
    DialogOverlay,
    DialogTitle,
    TransitionChild,
    TransitionRoot,
} from "@headlessui/vue";
import { ExclamationIcon } from "@heroicons/vue/outline";
import PrimaryButton from "@/Framework/Button/PrimaryButton.vue";
import MaterialIcon from "@/Framework/Icon/MaterialIcon.vue";
import { ref } from "vue";
defineOptions({
    name: "OptionDialog",
});
defineProps({
    options: { type: Array, default: () => [], required: false },
});

const dialog = ref(false);
const resolve = ref(null);
const reject = ref(null);
const message = ref(null);
const title = ref(null);

const show = (title_text, message_text) => {
    title.value = title_text;
    message.value = message_text;

    dialog.value = true;

    return new Promise((resolve_callback, reject_callback) => {
        resolve.value = resolve_callback;
        reject.value = reject_callback;
    });
};

const cancel = () => {
    resolve.value?.(undefined);
    dialog.value = false;
};
const agree = (value) => {
    resolve.value?.(value);
    dialog.value = false;
};

defineExpose({ show });
</script>
