<template>
    <tool-tip position="start" class="col-span-full flex justify-end">
        <span>{{ title ?? useTranslate("commons", "view") }}</span>
        <template #content>
            <button
                v-bind="$attrs"
                :dusk="dusk"
                @click="
                    () => (routeUrl ? $inertia.visit(routeUrl) : $emit('click'))
                "
            >
                <material-icon :color="color"> visibility</material-icon>
            </button>
        </template>
    </tool-tip>
</template>

<script setup>
import ToolTip from "@/Framework/ToolTip/ToolTip.vue";
import MaterialIcon from "@/Framework/Icon/MaterialIcon.vue";
import { useTranslate } from "@/Composables/Translate.js";

defineOptions({
    name: "ViewButton",
    inheritAttrs: false,
});

defineProps({
    routeUrl: { type: String, default: "", required: false },
    title: { type: String, default: null, required: false },
    dusk: { type: String, default: "view-button", required: false },
    color: { type: String, default: "primary", required: false },
});

defineEmits(["click"]);
</script>
