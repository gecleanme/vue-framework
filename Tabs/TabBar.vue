<template>
    <div
        class="w-full flex flex-col select-none ring-1 ring-primary-500 rounded-lg"
    >
        <div
            class="flex text-sm w-full snap-mandatory snap-x divide-x rtl:divide-x-reverse divide-primary-500 p-2 overflow-x-auto py-2"
        >
            <template v-for="(tab, index) in tabsVisibleGrouped" :key="tab.key">
                <button
                    :class="[
                        activeTab === tab.key ||
                        activeTabSubItems.find((t) => t.group === tab.key)
                            ? 'text-white bg-primary-500'
                            : 'text-gray-500',
                        { 'ltr:rounded-l-lg rtl:rounded-r-lg': index === 0 },
                        {
                            'ltr:rounded-r-lg rtl:rounded-l-lg':
                                index + 1 === tabsVisibleGrouped.length,
                        },
                        dense ? 'py-2' : 'py-4',
                    ]"
                    class="outline-primary-400 transition-all duration-300 min-w-0 flex-1 snap-start white px-4 font-medium text-center hover:bg-primary-400 hover:text-white"
                    @click="onTabClick(tab)"
                    :dusk="
                        tab.type === 'group'
                            ? `tab-group-${tab.key}`
                            : `tab-${tab.key}`
                    "
                >
                    {{ tab.title }}
                </button>
            </template>
        </div>
        <div
            class="flex w-full border-t border-gray-200"
            v-if="activeTabSubItems?.length"
        >
            <div
                class="flex w-full snap-mandatory snap-x divide-x rtl:divide-x-reverse divide-primary-500 p-2 overflow-x-auto py-2"
            >
                <template
                    v-for="(tab, index) in activeTabSubItems"
                    :key="tab.key"
                >
                    <button
                        :class="[
                            activeTab === tab.key
                                ? 'text-white bg-primary-500'
                                : 'text-gray-500',
                            {
                                'ltr:rounded-l-lg rtl:rounded-r-lg':
                                    index === 0,
                            },
                            {
                                'ltr:rounded-r-lg rtl:rounded-l-lg':
                                    index + 1 === activeTabSubItems.length,
                            },
                        ]"
                        class="outline-primary-400 transition-all duration-300 min-w-0 flex-1 snap-start white py-2 px-4 font-medium text-center hover:bg-primary-400 hover:text-white"
                        @click="onTabClick(tab)"
                        :dusk="`tab-${tab.key}`"
                    >
                        {{ tab.title }}
                    </button>
                </template>
            </div>
        </div>
    </div>
</template>

<script setup>
import { computed, ref } from "vue";
import { useSearchFilter } from "@/Composables/SearchFilter.js";
defineOptions({
    name: "TabBar",
});

const props = defineProps({
    modelValue: { type: String, default: null, required: false },
    persistTab: { type: Boolean, default: false, required: false },
    dense: { type: Boolean, default: false, required: false },
    tabs: {
        type: Array,
        default: () => [],
        required: true,
    },
});
const emit = defineEmits(["update:modelValue", "onTabChange"]);

const activeTab = ref(props.modelValue);
const tabsVisible = computed(() => {
    return props.tabs.filter((tab) => tab.visible);
});
const tabsVisibleGrouped = computed(() => {
    let items = [];
    props.tabs
        .filter((tab) => tab.visible)
        .forEach((tab) => {
            if (!tab.group) {
                items.push({ ...tab, type: "tab" });
                return;
            }
            const group_index = items.findIndex(
                (t) => t.type === "group" && t.key === tab.group,
            );
            if (group_index === -1) {
                items.push({
                    title: tab.group,
                    key: tab.group,
                    type: "group",
                    items: [tab],
                });
            } else {
                items[group_index].items.push({ ...tab, type: "tab" });
            }
        });
    return items;
});
const activeTabSubItems = computed(() => {
    let tab = tabsVisibleGrouped.value.find((t) => t.key === activeTab.value);
    if (!tab) {
        tab = tabsVisible.value.find((t) => t.key === activeTab.value);
    }
    if (!tab) {
        return [];
    }
    if (tab.type === "group") {
        return tab.items;
    }
    if (tab.group) {
        return tabsVisible.value.filter((t) => t.group === tab.group);
    }
    return [];
});

function onTabClick(tab) {
    let key = tab.key;
    if (tab.type === "group") {
        key = tab.items[0]?.key;
    }

    if (key !== activeTab.value) {
        emit("onTabChange", key);
    }

    activeTab.value = key;
    emit("update:modelValue", key);
    if (props.persistTab) {
        useSearchFilter({ tab: key }, [], { preserveScroll: true });
    }
}
</script>

<style scoped>
*::-webkit-scrollbar {
    width: 0.2em !important;
    height: 0.2em !important;
}

*::-webkit-scrollbar-track {
    -webkit-box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.3) !important;
}

*::-webkit-scrollbar-thumb {
    background-color: #22c55e !important;
    outline: 1px solid #22c55e !important;
}
</style>
