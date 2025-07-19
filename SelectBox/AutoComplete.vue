<template>
    <div class="flex flex-col w-full transition-all duration-300">
        <div class="flex w-full">
            <label
                v-if="label && label.length"
                :class="[{ 'text-red-400': errorMessage.length }]"
                class="text-gray-500 text-md px-1 select-none"
                >{{ label }}</label
            >
        </div>

        <Combobox
            v-model="selectedItem"
            as="div"
            class="relative inline-block w-full text-left"
            :class="[
                errorMessage && errorMessage.length
                    ? 'ring-red-400 ring-1 rounded-md'
                    : disabled || !items || items.length == 0
                      ? 'ring-gray-300 ring-1 rounded-md'
                      : 'ring-primary-300 ring-1 rounded-md',
            ]"
            :model-value="modelValueComputed"
            @update:model-value="
                (value) => {
                    $emit('update:modelValue', getReturnValue(value));
                    $emit('change', getReturnValue(value));
                }
            "
        >
            <div class="flex items-center">
                <slot name="prepend" />

                <ComboboxInput
                    class="ring-0 w-full border-primary-200 disabled:text-gray-400 border-0 focus:border-primary-200 rounded-lg focus:ring-0 placeholder:text-gray-500"
                    :default-value="search"
                    @change="(e) => (search = e.target.value)"
                    :displayValue="() => selectedLabel"
                    :placeholder="placeholder"
                    :disabled="disabled"
                />

                <button
                    v-if="clearable && !(!modelValue || disabled)"
                    :class="[{ 'opacity-0': !modelValue || disabled }]"
                    :disabled="disabled || !modelValue"
                    @click="
                        () => {
                            search = '';
                            $emit('update:modelValue', null);
                            $emit('change', null);
                        }
                    "
                    class="flex items-center pr-2 text-gray-400 select-none bg-white rounded-md"
                >
                    <material-icon :size="1"> close </material-icon>
                </button>

                <slot name="append" />
                <ComboboxButton
                    :disabled="disabled"
                    class="flex items-center pr-2 bg-white rounded-md"
                >
                    <material-icon
                        :color="
                            disabled
                                ? 'gray-lighten'
                                : errorMessage && errorMessage.length
                                  ? 'error'
                                  : 'primary'
                        "
                    >
                        expand_more
                    </material-icon>
                </ComboboxButton>
            </div>
            <ComboboxOptions
                class="absolute z-10 origin-top-right overflow-y-auto max-h-[15rem] top-12 min-w-full rounded-md bg-white shadow-lg ring-1 py-3 px-3 ring-black/5 focus:outline-hidden"
            >
                <ComboboxOption
                    v-for="item in filteredItems"
                    :key="item"
                    #default="{ active, selected }"
                    :value="item"
                >
                    <li
                        :class="[
                            active ? ' text-primary-500' : 'text-gray-900',
                            'group flex w-full items-center rounded-md px-2 py-1 text-sm',
                        ]"
                        class="capitalize cursor-pointer disabled:text-gray-100 whitespace-nowrap"
                    >
                        <slot :selected="selected" name="checked">
                            <material-icon
                                :size="0.5"
                                :class="{ 'opacity-0': !selected }"
                            >
                                done
                            </material-icon>
                        </slot>

                        <slot :item="item" name="item">
                            {{ getItemName(item) }}
                        </slot>
                    </li>
                </ComboboxOption>
                <div
                    class="flex justify-center"
                    v-if="
                        serversideFiltering && items.length < total && !loading
                    "
                >
                    <button @click="() => $emit('nextPage', ++currentPage)">
                        <material-icon> expand_more </material-icon>
                    </button>
                </div>
            </ComboboxOptions>
        </Combobox>
        <div class="break-words select-none">
            <p
                v-if="errorMessage.length"
                class="text-sm text-red-400 px-1 mt-1"
            >
                {{ errorMessage }}
            </p>
        </div>
    </div>
</template>

<script setup>
import { computed, ref, watch } from "vue";
import {
    Combobox,
    ComboboxButton,
    ComboboxInput,
    ComboboxOption,
    ComboboxOptions,
} from "@headlessui/vue";
import MaterialIcon from "@/Framework/Icon/MaterialIcon.vue";

defineOptions({
    name: "AutoComplete",
});

const props = defineProps({
    items: {
        type: Array,
        default: () => [],
    },
    itemKey: { type: String, default: null, required: false },
    itemName: { type: String, default: null, required: false },
    modelValue: { default: null, required: false },
    placeholder: { type: String, default: null, required: false },
    multiple: { type: Boolean, default: false, required: false },
    serversideFiltering: { type: Boolean, default: false, required: false },
    hideSelected: { type: Boolean, default: false, required: false },
    disabled: { type: Boolean, default: false, required: false },
    clearable: { type: Boolean, default: false, required: false },
    errorMessage: { type: String, default: "", required: false },
    label: { type: String, default: "", required: false },
    dense: { type: Boolean, default: false, required: false },
    loading: { type: Boolean, default: false, required: false },
    returnObject: { type: Boolean, default: false, required: false },
    total: { type: Number },
});

const emit = defineEmits([
    "change",
    "update:search",
    "update:modelValue",
    "nextPage",
]);

const search = ref("");

const filteredItems = computed(() => {
    if (props.serversideFiltering) {
        return props.items;
    }

    let itemsOriginal = props.items;

    if (props.hideSelected) {
        itemsOriginal = itemsOriginal.filter((item) => {
            return item[props.itemKey] != props.modelValue;
        });
    }

    if (!search.value || (search.value && search.value.length == 0))
        return itemsOriginal;
    return itemsOriginal.filter((item) => {
        if (props.itemName) {
            return item[props.itemName]
                .toLowerCase()
                .includes(search.value.toLowerCase());
        } else {
            return item.toLowerCase().includes(search.value.toLowerCase());
        }
    });
});

const selectedItem = ref(null);
const selectedLabel = ref(getSelectedLabel(props.modelValue));
const currentPage = ref(1);

const getReturnValue = (value) => {
    if (!props.returnObject) {
        if (typeof value === "object") {
            return value[props.itemKey];
        }
    }
    return value;
};
const modelValueComputed = computed(() => {
    if (!props.returnObject && typeof props.modelValue !== "object") {
        return props.items.find(
            (item) => item[props.itemKey] == props.modelValue,
        );
    }
    return props.modelValue;
});

function getSelectedItem() {
    if (!props.multiple) {
        return props.items.find((item) =>
            props.itemKey
                ? item[props.itemKey] == props.modelValue
                : item == props.modelValue,
        );
    } else {
        return props.items.filter((item) =>
            props.modelValue.includes(
                props.itemKey ? item[props.itemKey] : item,
            ),
        );
    }
}

function getSelectedLabel(newValue) {
    if (!newValue) return null;

    if (typeof newValue === "object") {
        return newValue[props.itemName];
    }

    if (props.itemName) {
        const selectedItem = props.items.find(
            (item) => item[props.itemKey] == newValue,
        );
        return selectedItem ? selectedItem[props.itemName] : newValue;
    } else {
        return newValue;
    }
}

function getItemName(item) {
    if (typeof item === "object") {
        if (props.itemName) {
            return item[props.itemName];
        } else {
            return item[props.itemKey];
        }
    }
    return item;
}

watch(
    () => search.value,
    () => {
        currentPage.value = 1;
        emit("update:search", search.value);
    },
);

watch(
    () => props.items,
    () => {
        selectedLabel.value = getSelectedLabel(props.modelValue);
    },
    {
        deep: true,
    },
);
watch(
    () => props.modelValue,
    (newValue) => {
        selectedLabel.value = getSelectedLabel(newValue);
        selectedItem.value = getSelectedItem();
    },
);
</script>

<style scoped>
input {
    width: 100%;
}
</style>
