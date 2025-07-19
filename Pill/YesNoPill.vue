<script setup>
import { useTranslate } from "@/Composables/Translate.js";
import { computed } from "vue";

defineOptions({
    name: "YesNoPill",
});

const props = defineProps({
    value: {
        type: Boolean,
        default: false,
    },
    yesLabel: {
        type: String,
        default: null,
    },
    noLabel: {
        type: String,
        default: null,
    },
    invertColors: {
        type: Boolean,
        default: false,
    },
    labelVariant: {
        type: String,
        default: "YES_NO",
        validator: function (value) {
            return [
                "YES_NO",
                "TRUE_FALSE",
                "ON_OFF",
                "ENABLED_DISABLED",
                "ACTIVE_INACTIVE",
                "POSITIVE_NEGATIVE",
            ].includes(value);
        },
    },
    size: {
        type: Number,
        default: 2,
        validator: function (value) {
            return [1, 2, 3, 4, 5].includes(value);
        },
    },
});

const yesStyle = computed(() => {
    return props.invertColors ? "badge-danger" : "badge-success";
});
const noStyle = computed(() => {
    return props.invertColors ? "badge-success" : "badge-danger";
});

const noLabels = {
    YES_NO: useTranslate("commons", "no"),
    TRUE_FALSE: useTranslate("commons", "false"),
    ON_OFF: useTranslate("commons", "off"),
    ENABLED_DISABLED: useTranslate("commons", "disabled"),
    ACTIVE_INACTIVE: useTranslate("commons", "inactive"),
    POSITIVE_NEGATIVE: useTranslate("commons", "negative"),
};
const yesLabels = {
    YES_NO: useTranslate("commons", "yes"),
    TRUE_FALSE: useTranslate("commons", "true"),
    ON_OFF: useTranslate("commons", "on"),
    ENABLED_DISABLED: useTranslate("commons", "enabled"),
    ACTIVE_INACTIVE: useTranslate("commons", "active"),
    POSITIVE_NEGATIVE: useTranslate("commons", "positive"),
};

const sizeClasses = {
    1: "text-xs",
    2: "text-sm",
    3: "text-lg",
    4: "text-xl",
    5: "text-2xl",
};
const sizeStyles = computed(() => {
    return sizeClasses[parseInt(props.size.toString())] ?? sizeClasses[2];
});

function getNoLabel() {
    return noLabels[props.labelVariant] ?? noLabels.YES_NO;
}

function getYesLabel() {
    return yesLabels[props.labelVariant] ?? yesLabels.YES_NO;
}
</script>

<template>
    <span
        class="badge badge-pill"
        :class="[value ? yesStyle : noStyle, sizeStyles]"
    >
        {{ value ? (yesLabel ?? getYesLabel()) : (noLabel ?? getNoLabel()) }}
    </span>
</template>

<style scoped>
@reference "../../../css/app.css";

.badge-success {
    @apply bg-primary-700 text-white;
}

.badge-danger {
    @apply bg-red-500 text-white;
}

.badge-pill {
    @apply px-2  font-semibold rounded-md;
}
</style>
