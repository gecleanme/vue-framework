<script setup>
import {
    Listbox,
    ListboxButton,
    ListboxOption,
    ListboxOptions,
} from "@headlessui/vue";
import SafePopper from "@/Framework/SafePopper.vue";
import { computed, ref } from "vue";
import MaterialIcon from "@/Framework/Icon/MaterialIcon.vue";
import { useArrayWrap } from "@/Composables/array_helpers.js";
import { useTranslate } from "@/Composables/Translate.js";

const props = defineProps({
    label: { type: String, default: null, required: false },
    errorMessage: { type: String, default: null, required: false },
    placeholder: { type: String, default: null, required: false },
    items: { type: Array, default: () => [], required: true },
    itemKey: { type: String, default: null, required: false },
    itemName: { type: String, default: null, required: false },
    disabled: { type: Boolean, default: false, required: false },
    dense: { type: Boolean, default: false, required: false },
    multiSelect: { type: Boolean, default: false, required: false },
    hideSelected: { type: Boolean, default: false, required: false },
    returnObject: { type: Boolean, default: false, required: false },
    dusk: { type: String, default: "", required: false },
    modelValue: { default: null, required: false },
    clearable: { type: Boolean, default: false, required: false },
    hasSearch: { type: Boolean, default: true, required: false },
    alignEnd: { type: Boolean, default: false, required: false },
});

defineEmits(["update:modelValue", "change"]);

const is_changed = ref(false);

const getModelValue = () => {
    if (props.multiSelect) {
        return parseModelValue();
    }
    return parseModelValue()[0] ?? null;
};

const parseModelValue = () => {
    return useArrayWrap(props.modelValue);
};

const searchQuery = ref(null);

const itemsFiltered = computed(() => {
    let itemsOriginal = props.items;

    if (props.hideSelected) {
        itemsOriginal = itemsOriginal.filter(() => {
            // todo
            // return item[props.itemKey] != props.modelValue
        });
    }

    if (
        !searchQuery.value ||
        (searchQuery.value && searchQuery.value.length === 0)
    )
        return itemsOriginal;
    return itemsOriginal.filter((item) => {
        return getItemLabel(item)
            .toString()
            .toLowerCase()
            .includes(searchQuery.value?.toLowerCase());
    });
});

const getItemLabel = (item) => {
    if (typeof item === "object") {
        return item[props.itemName] ?? item;
    }
    return item;
};
</script>

<template>
    <div class="flex flex-col w-full transition-all duration-300">
        <label
            :class="[{ 'text-red-400': errorMessage?.length }]"
            class="text-gray-500 text-md select-none"
            >{{ label }}
            <Listbox
                as="div"
                :dusk="dusk"
                class="relative inline-block w-full text-left"
                #default="{ open }"
                :multiple="multiSelect"
                :model-value="getModelValue(parseModelValue())"
                @update:model-value="
                    (value) => {
                        is_changed = true;
                        $emit('update:modelValue', value);
                    }
                "
            >
                <SafePopper
                    class="w-full border-none! m-0!"
                    :show="open"
                    :onOpen:popper="() => (is_changed = false)"
                    :onClose:popper="
                        () => {
                            if (is_changed) {
                                $emit('change', modelValue);
                            }
                        }
                    "
                >
                    <ListboxButton
                        :class="[
                            errorMessage && errorMessage.length
                                ? 'ring-red-400'
                                : disabled || !items?.length
                                  ? 'ring-gray-300'
                                  : 'ring-primary-300',
                            dense ? 'py-1' : 'py-2',
                        ]"
                        :disbled="disabled || !items?.length"
                        as="div"
                        class="select-none bg-white w-full items-center ring-1 px-3 rounded-md disabled:ring-gray-300 flex justify-between"
                        @click="searchQuery = null"
                        :dusk="`${dusk}-button`"
                        #default="{ value }"
                    >
                        <div class="">
                            {{
                                useArrayWrap(value).length
                                    ? useArrayWrap(value)
                                          .map((item) => getItemLabel(item))
                                          .join(", ")
                                    : (placeholder ??
                                      useTranslate("commons", "select"))
                            }}
                        </div>

                        <div class="flex items-center gap-1">
                            <div
                                v-if="
                                    clearable && useArrayWrap(modelValue).length
                                "
                                :class="[
                                    {
                                        'opacity-0':
                                            !useArrayWrap(modelValue).length ||
                                            disabled,
                                    },
                                ]"
                                class="text-gray-400"
                                @click.stop.prevent="
                                    () => {
                                        $emit('update:modelValue', null);
                                        $emit('change', null);
                                    }
                                "
                            >
                                <material-icon :size="2">close</material-icon>
                            </div>
                            <span
                                :class="[
                                    errorMessage && errorMessage.length
                                        ? 'text-red-300'
                                        : disabled ||
                                            !items ||
                                            items.length == 0
                                          ? 'text-gray-300'
                                          : 'text-primary-300',
                                ]"
                            >
                                <material-icon :size="2"
                                    >keyboard_arrow_down</material-icon
                                >
                            </span>
                        </div>
                    </ListboxButton>
                    <template #content>
                        <ListboxOptions
                            class="bg-white w-full origin-top-right overflow-y-auto max-h-[15rem] rounded-md shadow-lg ring-1 py-3 px-3 ring-black/5 focus:outline-hidden"
                            :class="[
                                alignEnd
                                    ? 'rtl:left-0 right-0'
                                    : 'rtl:right-0 left-0',
                            ]"
                        >
                            <ListboxOption
                                v-if="
                                    hasSearch &&
                                    items.length &&
                                    items.length > 4
                                "
                                disabled
                            >
                                <input
                                    v-model="searchQuery"
                                    class="w-full"
                                    type="text"
                                    @keydown.space.stop
                                />
                            </ListboxOption>
                            <ListboxOption
                                v-for="(item, index) in itemsFiltered"
                                :key="index"
                                #default="{ active, selected }"
                                :value="item"
                                :dusk="`${dusk}-option-${index}`"
                            >
                                <li
                                    :class="[
                                        active
                                            ? ' text-primary-500'
                                            : 'text-gray-900',
                                        'group flex w-full items-center rounded-md px-2 py-1 text-sm',
                                    ]"
                                    class="capitalize cursor-pointer disabled:text-gray-100 whitespace-nowrap gap-1"
                                >
                                    <slot :selected="selected" name="checked">
                                        <material-icon
                                            color="primary"
                                            :class="[
                                                selected
                                                    ? 'opacity-100'
                                                    : 'opacity-0',
                                            ]"
                                            :size="0.5"
                                        >
                                            done
                                        </material-icon>
                                    </slot>
                                    <slot :item="item" name="item">
                                        {{ getItemLabel(item) }}
                                    </slot>
                                </li>
                            </ListboxOption>
                        </ListboxOptions>
                    </template>
                </SafePopper>
            </Listbox>
        </label>
    </div>
</template>

<style scoped>
:deep(.popper) {
    min-width: 100%;
}
</style>
