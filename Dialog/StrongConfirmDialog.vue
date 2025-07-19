<template>
    <TransitionRoot
        :dir="$page.props.locale === 'en' ? 'ltr' : 'rtl'"
        :show="dialog"
        as="template"
    >
        <Dialog
            as="div"
            class="fixed z-50 inset-0 overflow-y-auto"
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
                            <div
                                class="mx-auto shrink-0 flex items-center justify-center h-12 w-12 rounded-full bg-red-100 sm:mx-0 sm:h-10 sm:w-10"
                            >
                                <ExclamationIcon
                                    aria-hidden="true"
                                    class="h-6 w-6 text-red-600"
                                />
                            </div>
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
                                    <div class="mt-4">
                                        <p class="text-sm text-gray-700">
                                            {{ instruction }}
                                        </p>
                                        <input
                                            v-model="inputValue"
                                            :placeholder="placeholder"
                                            class="mt-2 w-full border rounded px-3 py-2 focus:outline-none focus:ring-2 focus:ring-red-500"
                                            :dir="
                                                $page.props.locale === 'en'
                                                    ? 'ltr'
                                                    : 'rtl'
                                            "
                                            @keyup.enter="tryAgree"
                                            :autofocus="true"
                                        />
                                        <p
                                            v-if="inputValue && !isMatch"
                                            class="text-xs text-red-500 mt-1"
                                        >
                                            {{ mismatchMessage }}
                                        </p>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div
                            class="mt-5 sm:mt-4 flex flex-col lg:flex-row lg:flex-row-reverse justify-start lg:gap-2"
                        >
                            <button
                                class="w-full inline-flex justify-center rounded-md border border-transparent shadow-xs px-4 py-2 bg-red-600 text-base font-medium text-white hover:bg-red-700 focus:outline-hidden focus:ring-2 focus:ring-offset-2 focus:ring-red-500 sm:w-auto sm:text-sm"
                                type="button"
                                @click="tryAgree"
                                :disabled="!isMatch"
                                dusk="strong-confirm-dialog-agree-button"
                            >
                                {{ useTranslate("commons", "agree") }}
                            </button>
                            <button
                                ref="cancelButtonRef"
                                class="mt-3 w-full inline-flex justify-center rounded-md border border-gray-300 shadow-xs px-4 py-2 bg-white text-base font-medium text-gray-700 hover:bg-gray-50 focus:outline-hidden focus:ring-2 focus:ring-offset-2 focus:ring-gray-300 sm:mt-0 sm:w-auto sm:text-sm"
                                type="button"
                                @click="cancel"
                            >
                                {{ useTranslate("commons", "cancel") }}
                            </button>
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
import { useTranslate } from "@/Composables/Translate";
import { computed, ref } from "vue";

const dialog = ref(false);
const resolve = ref(null);
const reject = ref(null);
const message = ref(null);
const title = ref(null);
const requiredString = ref("");
const instruction = ref("");
const placeholder = ref("");
const mismatchMessage = ref("");
const inputValue = ref("");

const isMatch = computed(() => inputValue.value === requiredString.value);

const show = (
    title_text,
    message_text,
    required_string,
    instruction_text,
    placeholder_text,
    mismatch_message,
) => {
    title.value = title_text;
    message.value = message_text;
    requiredString.value = required_string;
    instruction.value = instruction_text;
    placeholder.value = placeholder_text;
    mismatchMessage.value = mismatch_message;
    inputValue.value = "";
    dialog.value = true;
    return new Promise((resolve_callback, reject_callback) => {
        resolve.value = resolve_callback;
        reject.value = reject_callback;
    });
};

const cancel = () => {
    resolve.value?.(false);
    dialog.value = false;
};
const tryAgree = () => {
    if (isMatch.value) {
        resolve.value?.(true);
        dialog.value = false;
    }
};
defineExpose({ show });
</script>
