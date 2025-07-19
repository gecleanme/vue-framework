<template>
    <modal-dialog dense v-model="videoPreviewDialog">
        <video class="w-full max-h-[90vh]" id="video-preview" controls />
    </modal-dialog>
    <modal-dialog dense v-model="documentPreviewDialog">
        <iframe
            id="fred"
            :src="document_preview"
            frameborder="1"
            scrolling="auto"
            class="h-[90vh] w-[80vw]"
        />
    </modal-dialog>
    <vue-easy-lightbox
        :visible="image_preview_visible"
        :imgs="image_preview"
        :index="image_preview_index"
        @hide="onHide"
    />
</template>

<script setup>
import ModalDialog from "@/Framework/ModalDialog/ModalDialog.vue";
import { ref } from "vue";
import VueEasyLightbox from "vue-easy-lightbox";
import {
    isAudioFile,
    isImageFile,
    isPreviewableDocumentFile,
    isVideoFile,
} from "@/Composables/file_helpers.js";
import { useAudioPlayer } from "@/Composables/ui/mediaPlayBack.js";

defineOptions({
    name: "FilePreview",
});

const { playAudio } = useAudioPlayer();
const image_preview = ref(null);
const image_preview_visible = ref(null);
const image_preview_index = ref(0);
const videoPreviewDialog = ref(false);
const documentPreviewDialog = ref(false);
const document_preview = ref(null);
const onHide = () => (image_preview_visible.value = false);

function previewAudioUrl(url, name) {
    playAudio(url, name);
}

function previewAudioFile(file) {
    playAudio(URL.createObjectURL(file), name ?? file.name);
}

function previewDocumentFile(file) {
    documentPreviewDialog.value = true;
    document_preview.value = URL.createObjectURL(file);
}

function previewDocumentUrl(url) {
    documentPreviewDialog.value = true;
    document_preview.value = url;
}

function previewImageUrl(url) {
    if (url) {
        image_preview.value = url;
        image_preview_visible.value = true;
    }
}
function previewImageFile(file) {
    if (file) {
        image_preview.value = URL.createObjectURL(file);
        image_preview_visible.value = true;
    }
}

function previewVideoFile(file) {
    videoPreviewDialog.value = true;
    setTimeout(() => {
        const video = document.getElementById("video-preview");
        const reader = new FileReader();

        reader.readAsDataURL(file);
        reader.addEventListener("load", function () {
            video.src = reader.result;
        });
    }, 2000);
}
function previewVideoUrl(url) {
    videoPreviewDialog.value = true;

    setTimeout(() => {
        const video = document.getElementById("video-preview");
        video.src = url;
    }, 2000);
}

const open = (file) => {
    if (!file) {
        return;
    }

    if (isPreviewableDocumentFile(file)) {
        previewDocumentFile(file);
        return;
    }
    if (isImageFile(file)) {
        previewImageFile(file);
        return;
    }
    if (isVideoFile(file)) {
        previewVideoFile(file);
        return;
    }
    if (isAudioFile(file)) {
        previewAudioFile(file);
    }
};
const openWithUrl = (url, type, name = "") => {
    if (!url || !type) {
        return;
    }

    switch (type) {
        case "audio":
            previewAudioUrl(url, name);
            break;
        case "image":
            previewImageUrl(url);
            break;

        case "video":
            previewVideoUrl(url);
            break;
        case "document":
            previewDocumentUrl(url);
            break;
    }
};
defineExpose({ openWithUrl, open });
</script>

<style scoped></style>
