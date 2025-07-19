<script setup>
import { useGenerateRandomId } from "@/Composables/generateRandomId.js";
import CheckBox from "@/Framework/CheckBox/CheckBox.vue";
import {
    useArrayPropertyCompare,
    useArrayWrap,
} from "@/Composables/array_helpers.js";
import { ref, watch } from "vue";
import MaterialIcon from "@/Framework/Icon/MaterialIcon.vue";

defineOptions({
    name: "CheckBoxGroup",
});
const props = defineProps({
    label: {
        type: String,
        default: null,
        required: false,
    },
    items: {
        type: Array,
        default: () => [],
        required: false,
    },
    modelValue: {
        default: null,
        required: false,
    },
    row: {
        type: Boolean,
        default: false,
        required: false,
    },
    clearable: {
        type: Boolean,
        default: false,
        required: false,
    },
    disabled: {
        type: Boolean,
        default: false,
        required: false,
    },
    multiSelect: {
        type: Boolean,
        default: false,
        required: false,
    },
    mandatory: {
        type: Boolean,
        default: false,
        required: false,
    },
    errorMessage: {
        type: String,
        default: "",
        required: false,
    },
    id: {
        type: String,
        default: () => useGenerateRandomId(),
        required: false,
    },
    itemKey: {
        type: String,
        default: "id",
        required: false,
    },
    itemName: {
        type: String,
        default: "name",
        required: false,
    },
});

const emit = defineEmits(["update:modelValue", "change"]);

const parseModelValue = () => {
    return useArrayWrap(props.modelValue);
};
const selectedData = ref(parseModelValue());

const onValueChange = (id, value) => {
    if (value) {
        selectedData.value.push(id);
    } else {
        selectedData.value = selectedData.value.filter((d) => d !== id);
    }
};

const getSelectedItems = () => {
    if (props.multiSelect) {
        return selectedData.value;
    } else {
        return selectedData.value[0] ?? null;
    }
};

watch(selectedData, () => {
    if (selectedData.value.length === 0 && props.mandatory) {
        const first = props.items[0];
        if (first) {
            selectedData.value.push(first[props.itemKey] ?? first);
        }
    }
    emit("update:modelValue", getSelectedItems());
});
watch(
    () => props.modelValue,
    () => {
        if (!useArrayPropertyCompare(parseModelValue(), selectedData.value)) {
            selectedData.value = parseModelValue();
        }
    },
);
</script>

<template>
    <fieldset class="py-3" :id="id">
        <legend class="sr-only">
            {{ label }}
        </legend>
        <div class="flex items-center py-1">
            <label
                :class="[{ 'text-red-400': errorMessage.length }]"
                class="text-gray-500 text-md px-1 select-none"
                >{{ label }}</label
            >
            <button
                v-if="clearable"
                :class="[{ 'opacity-0': !selectedData.length || disabled }]"
                :disabled="disabled || !selectedData.length"
                class="text-gray-400"
                @click="
                    () => {
                        $emit('update:modelValue', null);
                        $emit('change', null);
                        $emit('onClear', null);
                    }
                "
            >
                <material-icon :size="1">close</material-icon>
            </button>
        </div>
        <div class="gap-5" :class="[row ? 'flex' : 'flex flex-col']">
            <div
                v-for="item in items"
                :key="item.id"
                class="relative flex items-start"
            >
                <div class="flex items-center h-5">
                    <check-box
                        :label="item[itemName]"
                        @change="(value) => onValueChange(item[itemKey], value)"
                        :id="`${id}${item[itemKey]}`"
                        :aria-describedby="`${id}${item[itemKey]}-description`"
                        :name="id"
                        :model-value="selectedData.indexOf(item[itemKey]) > -1"
                        :class="[item.style]"
                    />
                </div>
            </div>
        </div>
        <div class="break-words select-none">
            <p
                v-if="errorMessage.length"
                class="text-sm text-red-400 px-1 mt-1"
            >
                {{ errorMessage }}
            </p>
        </div>
    </fieldset>
</template>

<style scoped></style>
