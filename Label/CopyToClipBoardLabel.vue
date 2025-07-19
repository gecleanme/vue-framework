<template>
    <div class="flex items-center gap-1">
        <slot :copied="copied">
            {{ label }}
        </slot>
        <button
            class="cursor-pointer"
            :class="{}"
            @click.prevent="copyToClipboard"
        >
            <material-icon :size="1" :color="copied ? 'primary' : 'gray'">
                {{ copied ? "done" : icon }}
            </material-icon>
        </button>
    </div>
</template>
<script setup>
import { ref } from "vue";
import MaterialIcon from "@/Framework/Icon/MaterialIcon.vue";

defineOptions({
    name: "CopyToClipBoardLabel",
});

const props = defineProps({
    label: { type: String, default: null, required: false },
    icon: { type: String, default: "content_copy", required: false },
});
const copied = ref(false);
const textToCopy = props.label;
// const notificationStore = ref(useLocalNotificationStore());
const copyToClipboard = async () => {
    try {
        await navigator.clipboard.writeText(textToCopy);
        copied.value = true;
        // notificationStore.value.addNotification("success", "Copied");
        setTimeout(() => {
            copied.value = false;
        }, 2000);
    } catch (error) {
        console.error("Unable to copy to clipboard:", error);
    }
};
</script>
