<template>
    <div class="">
        <SafePopper :placement="placement" :show="show">
            <template #content>
                <span
                    class="px-2 rounded-md py-1 text-sm text-center bg-gray-500 text-white opacity-90 transition-all duration-150 whitespace-nowrap"
                    ><slot
                /></span>
            </template>
            <div class="" @mouseleave="show = false" @mouseover="show = true">
                <slot name="content" />
            </div>
        </SafePopper>
    </div>
</template>

<script setup>
import SafePopper from "@/Framework/SafePopper.vue";
import { ref, watch } from "vue";
import { useTranslateToggle } from "@/Composables/TranslateToggle";

defineOptions({
    name: "ToolTip",
});
const props = defineProps({
    position: { type: String, default: "auto", required: false },
});

const show = ref(false);
const placement = ref(getPlacement(props.position));

function getPlacement(pos) {
    switch (pos) {
        case "start":
            return useTranslateToggle("left", "right");

        case "end":
            return useTranslateToggle("right", "left");

        case "top":
            return "top";

        case "bottom":
            return "bottom";

        default:
            return "auto";
    }
}

watch(
    () => props.position,
    (val) => {
        placement.value = getPlacement(val);
    },
);
</script>

<style scoped></style>
