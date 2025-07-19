<template>
    <modal-dialog>
        <template #title="scope">
            <div class="flex justify-between pb-3">
                <span class="text-primary-500 text-2xl font-semibold">
                    {{ useTranslate("store_pages", "preview") }}
                </span>
                <button @click.stop="scope.open(false)">
                    <material-icon color="error"> close </material-icon>
                </button>
            </div>
        </template>
        <template #activator="scope">
            <slot :open="scope.open" />
        </template>
        <div class="min-w-[60vw]">
            <dynamic-style-renderer :content="content" />
        </div>
    </modal-dialog>
</template>

<script setup>
import ModalDialog from "@/Framework/ModalDialog/ModalDialog.vue";
import MaterialIcon from "@/Framework/Icon/MaterialIcon.vue";
import DynamicStyleRenderer from "@/Layouts/DynamicStyleRenderer.vue";
import { useTranslate } from "@/Composables/Translate.js";

defineOptions({
    name: "TailwindDynamicContentPreview",
});
defineProps({
    content: { type: String, default: null, required: false },
});
</script>

<style scoped></style>
