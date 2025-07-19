<template>
    <div
        class="uploader"
        @dragenter="onDragEnter"
        @dragleave="onDragLeave"
        @dragover.prevent
        @drop="onDrop"
        :class="{ dragging: isDragging }"
    >
        <div class="empty-placeholder" :class="{ sendToBack: file }">
            <div class="upload-icon">
                <material-icon :size="7"> backup </material-icon>
            </div>
            <p>
                {{
                    useTranslate("file_drop_zone_single", "drag_your_file_here")
                }}
            </p>
            <div class="uppercase">
                {{ useTranslate("file_drop_zone_single", "or") }}
            </div>
            <div class="file-input">
                <label for="file" @click="openAttachmentDialog">
                    <primary-button depressed class="upload-button capitalize">
                        {{
                            useTranslate(
                                "file_drop_zone_single",
                                "select_your_file",
                            )
                        }}
                    </primary-button>
                    <input
                        type="file"
                        id="file"
                        class="hidden"
                        :accept="accept"
                        @change="onInputChanged"
                        ref="attachmentInputSingleRef"
                    />
                </label>
            </div>
        </div>

        <div
            class="files-preview"
            v-if="file"
            :class="{ fileTransparent: isDragging }"
        >
            <div class="img-wrapper">
                <div v-if="file" class="flex flex-col">
                    <material-icon :size="7" color="primary" class="mx-auto">
                        description
                    </material-icon>
                    <span class="text-xl text-gray-400">{{
                        file ? file.name : ""
                    }}</span>

                    <span class="text-xl text-gray-500">{{
                        useByteToReadable(file.size)
                    }}</span>
                </div>
                <div class="details">
                    <div class="actions">
                        <button @click="openAttachmentDialog">
                            <material-icon color="primary">
                                change_circle
                            </material-icon>
                        </button>
                        <button @click="removeFile" v-if="file">
                            <material-icon color="error">
                                delete
                            </material-icon>
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup>
import MaterialIcon from "@/Framework/Icon/MaterialIcon.vue";
import PrimaryButton from "@/Framework/Button/PrimaryButton.vue";
import { useTranslate } from "@/Composables/Translate.js";
import { useByteToReadable } from "@/Composables/byteToReadable.js";
import { ref, watch } from "vue";

defineOptions({
    name: "FileDropzoneSingle",
});

const props = defineProps({
    modelValue: {
        type: File,
        default: null,
    },
    accept: { type: String, default: "*/*", required: false },
});

const emit = defineEmits(["update:modelValue"]);

const file = ref(props.modelValue);
const attachmentInputSingleRef = ref(null);
const isDragging = ref(false);
const dragCounter = ref(0);

watch(
    () => file.value,
    () => {
        emit("update:modelValue", file.value);
    },
);
watch(
    () => props.modelValue,
    () => {
        file.value = props.modelValue;
    },
);

function onInputChanged(e) {
    const files = e.target.files;
    isDragging.value = false;
    if (files[0]) {
        addFile(files[0]);
    }
}

function onDragEnter(e) {
    e.preventDefault();
    dragCounter.value++;
    isDragging.value = true;
}

function onDragLeave(e) {
    e.preventDefault();
    dragCounter.value--;

    if (dragCounter.value <= 0) {
        isDragging.value = false;
    }
}

function onDrop(e) {
    e.preventDefault();
    e.stopPropagation();

    isDragging.value = false;

    const files = e.dataTransfer.files;
    if (files[0]) {
        addFile(files[0]);
    }
    dragCounter.value = 0;
}

function addFile(uploadFile) {
    file.value = uploadFile;
}

function openAttachmentDialog() {
    attachmentInputSingleRef.value.click();
}

function removeFile() {
    file.value = null;
}
</script>

<style scoped>
@reference "../../../css/app.css";

.uploader {
    @apply border-2 border-dotted border-primary-300 relative rounded-lg p-3 w-full aspect-4/1 flex flex-col items-center justify-center;
}

.empty-placeholder {
    @apply flex flex-col items-center gap-3 text-primary-500 font-semibold text-xl;
}

.dragging {
    @apply border-gray-300 bg-gray-400 text-white;
}

.dragging .empty-placeholder {
    @apply border-gray-300  text-white;
}

.upload-button {
    @apply font-bold;
}

.dragging .upload-button {
    @apply bg-white text-gray-500;
}

.files-preview {
    display: flex;
    flex-wrap: wrap;
    position: absolute;
    align-items: center;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
}

.img-wrapper {
    @apply p-2 w-full h-[80%];
}

.img-wrapper img {
    @apply object-contain  max-h-[80%] w-full;
}

.details {
    font-size: 12px;
    color: black;
    background: white;
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    padding: 3px 6px 10px;
    position: relative;
}

.name {
    overflow: hidden;
    height: 18px;
    text-align: start;
    width: 100%;
    padding-right: 5px;
}

.size {
}

.item-overlay {
}

.actions {
    @apply flex absolute bottom-0 right-0 justify-end gap-3;
}

.fileTransparent {
    @apply opacity-[0.5];
}

.sendToBack {
    @apply opacity-0 z-[-10];
}
</style>
