<template>
    <div :class="[{ 'flex ': !noStyles }, stylesClass]">
        <template v-for="(item, index) in items" :key="index">
            <slot
                name="item"
                :index="index"
                :item="item"
                :selected="activeItem == item[itemKey]"
                :itemLabel="item[itemName]"
                :itemId="item[itemKey]"
                :executer="selectItem"
            >
                <button
                    :disabled="disabled"
                    :class="[
                        { 'ltr:rounded-l-lg rtl:rounded-r-lg': index === 0 },
                        {
                            'ltr:rounded-r-lg rtl:rounded-l-lg':
                                index + 1 === items.length,
                        },

                        activeItem == item[itemKey]
                            ? (item.active_style ?? 'bg-primary-500 text-white')
                            : '',
                        {
                            'bg-gray-50 ring-gray-200 text-gray-600':
                                activeItem !== item[itemKey],
                        },
                        activeItem !== item[itemKey]
                            ? (item.inactive_style ??
                              'ring-1 ring-inset ring-primary-500')
                            : '',
                    ]"
                    class="transition-all duration-300 py-2 px-3 text-sm lg:text-md disabled:bg-gray-100 disabled:text-gray-300 disabled:ring-0 disabled:outline-0 disabled:border-0"
                    @click="selectItem(item[itemKey])"
                >
                    {{ item[itemName] }}
                </button>
            </slot>
        </template>
    </div>
</template>

<script setup>
import { ref, watch } from "vue";

defineOptions({
    name: "ButtonGroup",
});

const props = defineProps({
    items: {
        type: Array,
        default: () => [],
        required: false,
    },
    modelValue: { default: null, required: true },
    itemKey: { type: String, default: "value", required: false },
    itemName: { type: String, default: "label", required: false },
    stylesClass: { type: String, default: "", required: false },
    noStyles: { type: Boolean, default: false, required: false },
    disabled: { type: Boolean, default: false, required: false },
});

const emit = defineEmits(["update:modelValue"]);

const activeItem = ref(props.modelValue);

watch(
    () => props.modelValue,
    (value) => {
        activeItem.value = value;
    },
);

function selectItem(value) {
    activeItem.value = value;
    emit("update:modelValue", value);
}
</script>

<style scoped></style>
