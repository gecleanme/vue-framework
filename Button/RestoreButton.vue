<template>
    <tool-tip
        v-if="isRestorable"
        position="start"
        class="col-span-full flex justify-end"
    >
        <span>{{ title ?? useTranslate("commons", "restore") }}</span>
        <template #content>
            <confirmed-action
                :executor="
                    () => (routeUrl ? $inertia.visit(routeUrl) : $emit('click'))
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
                                      ? $inertia.visit(routeUrl)
                                      : $emit('click')
                    "
                >
                    <material-icon :color="color"> device_reset</material-icon>
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
import { computed } from "vue";

defineOptions({
    name: "RestoreButton",
    inheritAttrs: false,
});

const props = defineProps({
    routeUrl: { type: String, default: "", required: false },
    title: { type: String, default: null, required: false },
    canRestore: { type: Boolean, default: false, required: false },
    confirmed: { type: Boolean, default: true, required: false },
    dusk: { type: String, default: "restore-button", required: false },
    color: { type: String, default: "blue", required: false },
});

defineEmits(["click"]);

const isRestorable = computed(() => Boolean(props.canRestore));
</script>
