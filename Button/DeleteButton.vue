<template>
    <tool-tip
        v-if="canDelete"
        position="start"
        class="col-span-full flex justify-end"
    >
        <span>{{ title ?? useTranslate("commons", "delete") }}</span>
        <template #content>
            <confirmed-action
                :executor="
                    () =>
                        routeUrl ? $inertia.delete(routeUrl) : $emit('click')
                "
                #default="scope"
            >
                <button
                    v-bind="$attrs"
                    :dusk="dusk"
                    @click="
                        confirmed
                            ? scope.confirm()
                            : () =>
                                  routeUrl
                                      ? $inertia.delete(routeUrl)
                                      : $emit('click')
                    "
                >
                    <material-icon :color="color"> {{ icon }}</material-icon>
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
    name: "DeleteButton",
    inheritAttrs: false,
});

defineProps({
    routeUrl: { type: String, default: "", required: false },
    title: { type: String, default: null, required: false },
    canDelete: { type: Boolean, default: false, required: true },
    confirmed: { type: Boolean, default: true, required: false },
    dusk: { type: String, default: "delete-button", required: false },
    color: { type: String, default: "error", required: false },
    icon: { type: String, default: "delete", required: false },
});

defineEmits(["click"]);
</script>
