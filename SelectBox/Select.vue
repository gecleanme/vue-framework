<template>
    <div class="flex flex-col transition-all duration-300 min-w-[33%]">
        <div class="flex w-full">
            <label
                v-if="label && label.length"
                :class="[{ 'text-red-400': errorMessage.length }]"
                class="text-gray-500 text-md px-1 select-none"
                >{{ label }}</label
            >
        </div>
        <div class="flex items-center gap-x-2">
            <Listbox
                :disabled="disabled || !items || items.length == 0"
                :model-value="modelValue"
                :multiple="multiple"
                as="div"
                :dusk="dusk"
                class="relative inline-block w-full text-left"
                @update:model-value="
                    (value) => {
                        $emit('update:modelValue', value);
                        $emit('change', value);
                    }
                "
                #default="{ open }"
            >
                <SafePopper
                    v-if="$page"
                    :placement="
                        $page.props.locale === 'en'
                            ? 'bottom-start'
                            : 'bottom-end'
                    "
                    :dir="$page.props.locale === 'en' ? 'ltr' : 'rtl'"
                    :show="open"
                    class="w-full border-none! m-0!"
                >
                    <ListboxButton
                        :class="[
                            errorMessage && errorMessage.length
                                ? 'ring-red-400'
                                : disabled || !items || items.length == 0
                                  ? 'ring-gray-300'
                                  : 'ring-primary-300',
                            dense ? 'py-1' : 'py-2',
                            buttonStyles,
                        ]"
                        :disbled="disabled || !items || items.length == 0"
                        as="div"
                        class="select-none bg-white w-full flex items-center ring-1 px-3 rounded-md disabled:ring-gray-300"
                        @click="search = null"
                        :dusk="`${dusk}-button`"
                    >
                        <div
                            :class="[
                                errorMessage && errorMessage.length
                                    ? 'text-red-500'
                                    : disabled || !items || items.length == 0
                                      ? 'text-gray-300'
                                      : 'text-gray-500',
                            ]"
                            class="mx-1 truncate w-full capitalize select-none outline-hidden flex whitespace-nowrap"
                        >
                            <slot
                                v-if="modelValue || showPlaceholderSlot"
                                :selected="selectedItem"
                                :selectedLabel="selectedLabel"
                                name="selected"
                            >
                                {{ selectedLabel }}
                            </slot>
                            <span v-else class="whitespace-nowrap"
                                >{{
                                    placeholder ??
                                    useTranslate("commons", "select")
                                }}<span
                                    v-if="items.length == 0"
                                    class="text-xs text-gray-500"
                                    >-
                                    {{
                                        useTranslate("commons", "no_items")
                                    }}</span
                                ></span
                            >
                        </div>
                        <slot name="append" />
                        <button
                            v-if="clearable && !(disabled || !modelValue)"
                            :class="[{ 'opacity-0': !modelValue || disabled }]"
                            :disabled="disabled || !modelValue"
                            class="text-gray-400 inline-flex items-center"
                            @click="
                                () => {
                                    $emit('update:modelValue', null);
                                    $emit('change', null);
                                    $emit('onClear', null);
                                }
                            "
                        >
                            <material-icon :size="2">close</material-icon>
                        </button>

                        <span
                            :class="[
                                errorMessage && errorMessage.length
                                    ? 'text-red-300'
                                    : disabled || !items || items.length == 0
                                      ? 'text-gray-300'
                                      : 'text-primary-300',
                            ]"
                            class="inline-flex items-center"
                        >
                            <material-icon :size="2">
                                keyboard_arrow_down</material-icon
                            >
                        </span>
                        <slot name="prepend-button" />
                    </ListboxButton>
                    <template #content>
                        <ListboxOptions
                            class="w-full z-10 origin-top-right overflow-y-auto max-h-[15rem] bg-white min-w-full rounded-md shadow-lg ring-1 py-3 px-3 ring-black/5 focus:outline-hidden"
                            :class="[
                                alignEnd
                                    ? 'rtl:left-0 right-0'
                                    : 'rtl:right-0 left-0',
                            ]"
                        >
                            <slot
                                name="prepend-top"
                                :items="items"
                                :selected="selectedItem"
                            />
                            <ListboxOption
                                v-if="
                                    hasSearch &&
                                    items.length &&
                                    items.length > 4
                                "
                                disabled
                            >
                                <text-field
                                    v-model="search"
                                    autofocus
                                    class="w-full"
                                    type="text"
                                    @keydown.space.stop
                                />
                            </ListboxOption>

                            <ListboxOption
                                v-if="hasPrepend"
                                #default="{ active, selected }"
                            >
                                <li
                                    :class="[
                                        active
                                            ? ' text-primary-500'
                                            : 'text-gray-900',
                                        'group flex w-full items-center rounded-md px-2 py-1 text-sm',
                                    ]"
                                    class="capitalize cursor-pointer disabled:text-gray-100 whitespace-nowrap"
                                >
                                    <slot
                                        :items="items"
                                        :selected="selectedItem"
                                        name="prepend-checked"
                                    >
                                        <CheckIcon
                                            :class="[
                                                selected
                                                    ? 'opacity-100'
                                                    : 'opacity-0',
                                            ]"
                                            class="w-4 h-4 text-primary-500"
                                        />
                                    </slot>
                                    <slot
                                        :items="items"
                                        :selected="selectedItem"
                                        name="prepend"
                                    />
                                </li>
                            </ListboxOption>

                            <ListboxOption
                                v-for="(item, index) in itemsFiltered"
                                :key="index"
                                #default="{ active, selected }"
                                :value="getItemValue(item)"
                                :dusk="`${dusk}-option-${index}`"
                            >
                                <li
                                    :class="[
                                        active
                                            ? ' text-primary-500'
                                            : 'text-gray-900',
                                        'group flex w-full items-center rounded-md px-2 py-1 text-sm',
                                    ]"
                                    class="capitalize cursor-pointer disabled:text-gray-100 whitespace-nowrap"
                                >
                                    <slot :selected="selected" name="checked">
                                        <CheckIcon
                                            :class="[
                                                selected
                                                    ? 'opacity-100'
                                                    : 'opacity-0',
                                            ]"
                                            class="w-4 h-4 text-primary-500"
                                        />
                                    </slot>
                                    <slot
                                        :item="item"
                                        name="item"
                                        :selected="selectedItem"
                                    >
                                        {{ getItemName(item) }}
                                    </slot>
                                </li>
                            </ListboxOption>
                        </ListboxOptions>
                    </template>
                </SafePopper>
            </Listbox>
        </div>
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
import {
    Listbox,
    ListboxButton,
    ListboxOption,
    ListboxOptions,
} from "@headlessui/vue";
import { CheckIcon } from "@heroicons/vue/outline";
import { computed, ref, watch } from "vue";
import { useTranslate } from "@/Composables/Translate.js";
import SafePopper from "@/Framework/SafePopper.vue";
import TextField from "@/Framework/TextField/TextField.vue";
import MaterialIcon from "@/Framework/Icon/MaterialIcon.vue";

defineOptions({
    name: "SelectInput",
});

const props = defineProps({
    errorMessage: { type: String, default: "", required: false },
    modelValue: { default: null, required: false },
    label: { type: String, default: "", required: false },
    placeholder: { type: String, default: null, required: false },
    items: { type: Array, default: () => [], required: false },
    itemKey: { type: String, default: null, required: false },
    itemName: { type: String, default: null, required: false },
    hasSearch: { type: Boolean, default: true, required: false },
    disabled: { type: Boolean, default: false, required: false },
    showPlaceholderSlot: { type: Boolean, default: false, required: false },
    clearable: { type: Boolean, default: false, required: false },
    multiple: { type: Boolean, default: false, required: false },
    hasPrepend: { type: Boolean, default: false, required: false },
    hideSelected: { type: Boolean, default: false, required: false },
    alignEnd: { type: Boolean, default: false, required: false },
    dense: { type: Boolean, default: false, required: false },
    dusk: { type: String, default: "", required: false },
    buttonStyles: { type: String, default: "", required: false },
});

defineEmits(["update:modelValue", "change", "onClear"]);

const items = ref(props.items);
const selectedItem = ref(getSelectedItem());

watch(
    () => props.items,
    (newVal) => {
        items.value = newVal;
        selectedLabel.value = getSelectedLabel(props.modelValue);
        selectedItem.value = getSelectedItem();
    },
);
const selectedLabel = ref(getSelectedLabel(props.modelValue));
watch(
    () => props.modelValue,
    (newValue) => {
        selectedLabel.value = getSelectedLabel(newValue);
        selectedItem.value = getSelectedItem();
    },
);

function getSelectedItem() {
    if (!props.multiple) {
        return items.value.find((item) =>
            props.itemKey
                ? item[props.itemKey] == props.modelValue
                : item == props.modelValue,
        );
    } else {
        return items.value.filter((item) =>
            props.modelValue.includes(
                props.itemKey ? item[props.itemKey] : item,
            ),
        );
    }
}

function getSelectedLabel(newValue) {
    if (!newValue) return props.placeholder;
    else if (props.itemName) {
        const selectedItem = items.value.find(
            (item) => item[props.itemKey] == newValue,
        );
        return selectedItem ? selectedItem[props.itemName] : newValue;
    } else {
        return newValue;
    }
}

const search = ref(null);

const itemsFiltered = computed(() => {
    let itemsOriginal = items.value;

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

function getItemValue(item) {
    if (typeof item === "object") {
        if (props.itemKey) {
            return item[props.itemKey];
        } else {
            return item;
        }
    } else {
        return item;
    }
}
</script>

<style scoped>
.multi-lingual-form .costum-select-option-style {
    position: relative !important;
    top: 0.8rem !important;
}
:deep(.popper) {
    min-width: 100%;
}
</style>
