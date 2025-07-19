<template>
    <div
        class="uploader"
        @dragenter="onDragEnter"
        @dragleave="onDragLeave"
        @dragover.prevent
        @drop="onDrop"
        :class="{ dragging: isDragging }"
    >
        <div
            class="empty-placeholder"
            :class="{ sendToBack: imagePreview || imageUrl }"
        >
            <div class="upload-icon">
                <material-icon :size="7"> backup </material-icon>
            </div>
            <p>
                {{ useTranslate("drop_zone_single", "drag_your_image_here") }}
            </p>
            <div class="uppercase">
                {{ useTranslate("drop_zone_single", "or") }}
            </div>
            <div class="file-input">
                <label for="file" @click="openAttachmentDialog">
                    <primary-button depressed class="upload-button capitalize">
                        {{
                            useTranslate(
                                "drop_zone_single",
                                "select_your_image",
                            )
                        }}
                    </primary-button>
                    <input
                        type="file"
                        id="file"
                        class="hidden"
                        accept="image/*"
                        @change="onInputChanged"
                        ref="attachmentInputSingleRef"
                    />
                </label>
            </div>
        </div>

        <div
            class="images-preview"
            v-if="imagePreview || imageUrl"
            :class="{ imageTransparent: isDragging }"
        >
            <div class="img-wrapper">
                <img
                    draggable="false"
                    :src="imagePreview ? imagePreview : imageUrl"
                    :alt="`Image Uploader`"
                />
                <div class="details">
                    <span class="name">{{ file ? file.name : "" }}</span>

                    <span class="size" v-if="file">{{
                        useByteToReadable(file.size)
                    }}</span>
                    <div class="actions">
                        <button @click="openAttachmentDialog">
                            <material-icon color="primary">
                                change_circle
                            </material-icon>
                        </button>
                        <button
                            @click="
                                () =>
                                    filePreviewRef.openWithUrl(
                                        imagePreview || imageUrl,
                                        'image',
                                    )
                            "
                        >
                            <material-icon color="primary">
                                fullscreen
                            </material-icon>
                        </button>
                        <button
                            @click="removeImage"
                            v-if="imagePreview || imageUrl"
                        >
                            <material-icon color="error">
                                delete
                            </material-icon>
                        </button>
                    </div>
                </div>
            </div>
        </div>

        <file-preview ref="filePreviewRef" />
    </div>
</template>

<script setup>
import MaterialIcon from "@/Framework/Icon/MaterialIcon.vue";
import PrimaryButton from "@/Framework/Button/PrimaryButton.vue";
import { useTranslate } from "@/Composables/Translate.js";
import { useByteToReadable } from "@/Composables/byteToReadable.js";
import { computed, ref, watch } from "vue";
import FilePreview from "@/Framework/Preview/FilePreview.vue";

defineOptions({
    name: "DropzoneSingle",
});

const props = defineProps({
    imageUrl: {
        type: String,
        default: null,
    },
    modelValue: {
        type: File,
        default: null,
    },
});

const emit = defineEmits(["update:modelValue", "onDeleteImageUrl"]);

const file = ref(props.modelValue);
const attachmentInputSingleRef = ref(null);
const isDragging = ref(false);
const dragCounter = ref(0);
const filePreviewRef = ref(null);

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
        addImage(files[0]);
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
        addImage(files[0]);
    }
    dragCounter.value = 0;
}

function addImage(imgFile) {
    if (!imgFile.type.match("image.*")) {
        return;
    }
    file.value = imgFile;
}

function openAttachmentDialog() {
    attachmentInputSingleRef.value.click();
}

function removeImage() {
    if (file.value) {
        file.value = null;
    } else {
        emit("onDeleteImageUrl");
    }
}

const imagePreview = computed(() => {
    if (!file.value) return null;

    return URL.createObjectURL(file.value);
});
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

.images-preview {
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

.imageTransparent {
    @apply opacity-[0.5];
}

.sendToBack {
    @apply opacity-0 z-[-10];
}
</style>
