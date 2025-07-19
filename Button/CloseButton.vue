<template>
    <tool-tip position="start" class="col-span-full flex justify-end">
        <span>{{ title ?? useTranslate("commons", "close") }}</span>
        <template #content>
            <confirmed-action :executor="() => $emit('click')" #default="scope">
                <button
                    v-bind="$attrs"
                    :dusk="dusk"
                    @click="
                        () => (confirmed ? scope.confirm() : $emit('click'))
                    "
                >
                    <material-icon :color="color"> close</material-icon>
                </button>
            </confirmed-action>
        </template>
    </tool-tip>
</template>

<script setup>
import ToolTip from "@/Framework/ToolTip/ToolTip.vue";
import MaterialIcon from "@/Framework/Icon/MaterialIcon.vue";
import { useTranslate } from "@/Composables/Translate.js";
import ConfirmedAction from "@/Layouts/components/dialogs/ConfirmedAction.vue";

defineOptions({
    name: "CloseButton",
    inheritAttrs: false,
});

defineProps({
    routeUrl: { type: String, default: "", required: false },
    title: { type: String, default: null, required: false },
    confirmed: { type: Boolean, default: false, required: false },
    dusk: { type: String, default: "close-button", required: false },
    color: { type: String, default: "error", required: false },
});

defineEmits(["click"]);
</script>
