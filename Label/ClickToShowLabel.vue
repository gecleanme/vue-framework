<template>
    <div class="flex items-center gap-1">
        <slot :showing="showing" v-if="showing">
            {{ label }}
        </slot>
        <button v-if="!showing" @click.prevent="showLabel">
            <material-icon :size="1"> visibility </material-icon>
        </button>
    </div>
</template>
<script setup>
import { ref } from "vue";
import MaterialIcon from "@/Framework/Icon/MaterialIcon.vue";

defineOptions({
    name: "ClickToShowLabel",
});

const props = defineProps({
    label: { type: String, default: null, required: false },
});

const showing = ref(false);
const textToCopy = props.label;
const showLabel = async () => {
    try {
        await navigator.clipboard.writeText(textToCopy);
        showing.value = true;
        setTimeout(() => {
            showing.value = false;
        }, 10000);
    } catch (error) {
        console.error("Unable to copy to clipboard:", error);
    }
};
</script>
