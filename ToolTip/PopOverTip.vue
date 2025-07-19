<template>
    <div class="">
        <SafePopper :placement="placement" :show="show">
            <template #content>
                <div
                    class="transition-all duration-300"
                    @mouseleave="show = false"
                    @mouseover="show = true"
                >
                    <slot />
                </div>
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

defineOptions({
    name: "PopOverTip",
});

const props = defineProps({
    position: { type: String, default: "auto", required: false },
});

const show = ref(false);
const placement = ref(getPlacement(props.position));

function getPlacement(pos) {
    switch (pos) {
        case "start":
            return "left";

        case "end":
            return "right";

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
