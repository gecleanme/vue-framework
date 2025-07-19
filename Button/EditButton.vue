<template>
    <tool-tip
        v-if="canEdit"
        position="start"
        class="col-span-full flex justify-end"
    >
        <span>{{ title ?? useTranslate("commons", "edit") }}</span>
        <template #content>
            <button
                v-bind="$attrs"
                :dusk="dusk"
                @click="
                    () => (routeUrl ? $inertia.visit(routeUrl) : $emit('click'))
                "
            >
                <material-icon :color="color"> edit</material-icon>
            </button>
        </template>
    </tool-tip>
</template>

<script setup>
import ToolTip from "@/Framework/ToolTip/ToolTip.vue";
import MaterialIcon from "@/Framework/Icon/MaterialIcon.vue";
import { useTranslate } from "@/Composables/Translate.js";

defineOptions({
    name: "EditButton",
    inheritAttrs: false,
});

defineProps({
    routeUrl: { type: String, default: "", required: false },
    title: { type: String, default: null, required: false },
    canEdit: { type: Boolean, default: false, required: true },
    dusk: { type: String, default: "edit-button", required: false },
    color: { type: String, default: "primary", required: false },
});

defineEmits(["click"]);
</script>
