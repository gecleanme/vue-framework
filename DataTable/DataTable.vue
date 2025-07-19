<template>
    <div class="w-full p-2">
        <div
            :class="[
                'rounded-lg p-2 ring-1 ring-black/10 print:h-20 print:ring-0',
                wrapperStyles,
            ]"
        >
            <div class="flex justify-center lg:mx-6">
                <slot name="top" />
            </div>
            <div
                class="flex justify-end py-1 gap-3 items-center lg:mx-6 print:hidden"
            >
                <slot name="tools" />
                <div class="inline-flex gap-3 items-center">
                    <filter-toggle v-if="canHideFilters" />
                    <Listbox
                        v-if="canHideColumns"
                        v-model="visibleColumns"
                        as="div"
                        class="relative w-full text-left"
                        multiple
                        @update:model-value="updateHiddenColumns"
                        #default="{ open }"
                    >
                        <SafePopper
                            :placement="
                                $page.props.locale === 'en'
                                    ? 'bottom-end'
                                    : 'bottom-start'
                            "
                            :dit="$page.props.locale === 'en' ? 'ltr' : 'rtl'"
                            :show="open"
                            class="w-full border-none! m-0!"
                        >
                            <ListboxButton
                                class="mx-1 w-full select-none rounded-md bg-white px-3 text-gray-500 outline-hidden ring-1 ring-primary-500"
                            >
                                {{ useTranslate("commons", "columns") }}
                            </ListboxButton>
                            <template #content>
                                <ListboxOptions
                                    class="w-fit origin-top-right rounded-md bg-white px-3 py-3 shadow-lg ring-1 ring-black/5 focus:outline-hidden"
                                >
                                    <ListboxOption
                                        v-for="(
                                            header_column, index
                                        ) in headers"
                                        :key="index"
                                        #default="{ active, selected }"
                                        :value="header_column.value"
                                    >
                                        <li
                                            :class="[
                                                active
                                                    ? ' text-primary-500'
                                                    : 'text-gray-900',
                                                'group flex w-full items-center rounded-md px-2 py-1 text-sm',
                                            ]"
                                            class="cursor-pointer whitespace-nowrap capitalize disabled:text-gray-100"
                                        >
                                            <CheckIcon
                                                :class="[
                                                    selected
                                                        ? 'opacity-100'
                                                        : 'opacity-0',
                                                ]"
                                                class="h-4 w-4 text-primary-500"
                                            />
                                            {{ header_column.label }}
                                        </li>
                                    </ListboxOption>
                                </ListboxOptions>
                            </template>
                        </SafePopper>
                    </Listbox>

                    <a v-if="canDownloadCsv" :href="csvUrl">
                        <material-icon color="primary">
                            download
                        </material-icon>
                    </a>
                </div>
            </div>
            <div class="flex flex-col">
                <div class="overflow-x-auto sm:-mx-2 print:overflow-visible">
                    <div
                        :class="[dense ? 'lg:px-2' : 'lg:px-8']"
                        class="inline-block min-w-full py-2 align-middle sm:px-4 md:px-6"
                    >
                        <div
                            :class="[
                                dense
                                    ? 'lg:shadow-none'
                                    : 'rounded-lg lg:shadow-sm lg:ring-1',
                            ]"
                            class="overflow-hidden lg:ring-black/5 print:overflow-visible"
                        >
                            <div
                                v-if="
                                    (mode === 'responsive' &&
                                        breakpoints.lgAndUp) ||
                                    mode === 'desktop' ||
                                    breakpoints.isPrint
                                "
                            >
                                <table
                                    :class="[
                                        { 'divide-y': !hideDefaultHeader },
                                    ]"
                                    class="min-w-full divide-gray-300"
                                >
                                    <thead
                                        :class="[
                                            { hidden: hideDefaultHeader },
                                            {
                                                'hidden lg:table-header-group':
                                                    hideDefaultHeaderInMobile,
                                            },
                                            {
                                                'lg:hidden':
                                                    hideDefaultHeaderInLarge,
                                            },
                                        ]"
                                        class="bg-gray-50"
                                    >
                                        <tr class="">
                                            <th
                                                v-for="(
                                                    heading, index
                                                ) in headersVisible"
                                                :key="heading.label + index"
                                                :class="[
                                                    heading.align ??
                                                        'text-left rtl:text-right',
                                                    heading.sortable
                                                        ? 'cursor-pointer hover:text-gray-600 '
                                                        : '',
                                                    options.sortBy ===
                                                        heading.value ||
                                                    options.sortBy ===
                                                        heading.sort_column
                                                        ? 'font-bold text-gray-600'
                                                        : 'text-gray-500',
                                                ]"
                                                class="select-none py-3.5 pl-4 pr-3 text-sm font-semibold"
                                                scope="col"
                                                @click="sort(heading)"
                                            >
                                                <div
                                                    class="group inline-flex transition-all"
                                                >
                                                    <slot
                                                        :heading="heading"
                                                        :name="
                                                            'heading.' +
                                                            heading.value
                                                        "
                                                        class="h-full"
                                                    >
                                                        <span class=" ">
                                                            {{ heading.label }}
                                                        </span>
                                                    </slot>
                                                    <span
                                                        :class="[
                                                            {
                                                                hidden: !heading.sortable,
                                                            },
                                                            {
                                                                'rotate-180':
                                                                    options.sortDesc,
                                                            },
                                                            options.sortBy ===
                                                                heading.value ||
                                                            options.sortBy ===
                                                                heading.sort_column
                                                                ? ''
                                                                : 'opacity-0 ',
                                                            heading.sortable
                                                                ? 'group-hover:opacity-50'
                                                                : 'w-0',
                                                        ]"
                                                        class="text-gray-400 transition-all"
                                                        ><material-icon
                                                            :size="2"
                                                            >expand_more</material-icon
                                                        ></span
                                                    >
                                                </div>
                                            </th>
                                        </tr>
                                    </thead>
                                    <tbody class="divide-gray-200 bg-white">
                                        <template
                                            v-for="(
                                                item, index
                                            ) in itemsPaginated"
                                            :key="index"
                                        >
                                            <tr
                                                :class="[
                                                    {
                                                        'cursor-pointer':
                                                            onRowItemClick,
                                                    },
                                                    rowStyleCallback
                                                        ? rowStyleCallback(item)
                                                        : '',
                                                ]"
                                                class="hover:bg-gray-50"
                                                @click="onRowClick(item, index)"
                                            >
                                                <td
                                                    v-for="(
                                                        heading, h_index
                                                    ) in headersVisible"
                                                    :key="
                                                        heading.label + h_index
                                                    "
                                                    :class="[
                                                        heading.align ??
                                                            'text-left rtl:text-right',
                                                        {
                                                            'border-t border-gray-200':
                                                                !$slots[
                                                                    'item.extend'
                                                                ],
                                                        },
                                                    ]"
                                                    class="break-words py-2 pl-4 pr-3 text-sm text-gray-600 sm:pl-6"
                                                >
                                                    <slot
                                                        :heading="heading"
                                                        :index="index"
                                                        :item="item"
                                                        :name="
                                                            'item.' +
                                                            heading.value
                                                        "
                                                    >
                                                        <span
                                                            v-if="heading.html"
                                                        >
                                                            <span
                                                                v-if="
                                                                    heading.data_accessor
                                                                "
                                                                v-html="
                                                                    heading.data_accessor(
                                                                        item,
                                                                    )
                                                                "
                                                            />
                                                            <span
                                                                v-else
                                                                v-html="
                                                                    item[
                                                                        heading
                                                                            .value
                                                                    ]
                                                                "
                                                            />
                                                        </span>
                                                        <span
                                                            v-else-if="
                                                                heading.data_accessor
                                                            "
                                                        >
                                                            {{
                                                                heading.data_accessor(
                                                                    item,
                                                                )
                                                            }}
                                                        </span>
                                                        <span v-else>
                                                            {{
                                                                item[
                                                                    heading
                                                                        .value
                                                                ]
                                                            }}
                                                        </span>
                                                    </slot>
                                                </td>
                                            </tr>
                                            <tr class="">
                                                <td
                                                    :colspan="
                                                        headersVisible.length
                                                    "
                                                >
                                                    <slot
                                                        :index="index"
                                                        :item="item"
                                                        name="item.extend"
                                                    />
                                                </td>
                                            </tr>

                                            <slot
                                                :index="index"
                                                :item="item"
                                                name="item.extend-row"
                                            />
                                        </template>

                                        <tr v-if="!itemsPaginated.length">
                                            <td
                                                class="px-3 py-2 text-center"
                                                colspan="100"
                                            >
                                                <slot name="empty">
                                                    <span class="text-gray-500">
                                                        <template
                                                            v-if="
                                                                searchField &&
                                                                searchField.length
                                                            "
                                                        >
                                                            No results found for
                                                            <span
                                                                class="text-gray-700"
                                                                >"{{
                                                                    searchField
                                                                }}"</span
                                                            >
                                                        </template>
                                                        <template v-else>
                                                            {{
                                                                loading
                                                                    ? loadingText
                                                                    : (noDataText ??
                                                                      useTranslate(
                                                                          "commons",
                                                                          "no_data_found",
                                                                      ))
                                                            }}
                                                        </template>
                                                    </span>
                                                </slot>
                                            </td>
                                        </tr>
                                    </tbody>
                                </table>
                            </div>

                            <div
                                v-if="
                                    (mode === 'responsive' &&
                                        breakpoints.mdAndDown) ||
                                    mode === 'mobile'
                                "
                                class="divide-y rounded-md bg-white ring-1 ring-gray-300 print:hidden"
                            >
                                <div
                                    v-for="(item, index) in itemsPaginated"
                                    :key="index"
                                    :class="[
                                        { 'cursor-pointer': onRowItemClick },
                                    ]"
                                    class="flex flex-col space-y-3 px-4 py-3 transition-all hover:bg-gray-100"
                                    @click="onRowClick(item)"
                                >
                                    <div
                                        v-for="(
                                            heading, h_index
                                        ) in headersVisible"
                                        :key="heading.label + h_index"
                                        class="flex w-full justify-between gap-1"
                                    >
                                        <span
                                            :class="[
                                                heading.sortable
                                                    ? 'cursor-pointer hover:text-gray-900 '
                                                    : '',
                                                options.sortBy ===
                                                    heading.value ||
                                                options.sortBy ===
                                                    heading.sort_column
                                                    ? 'font-bold text-gray-900'
                                                    : 'text-gray-500',
                                            ]"
                                            class="flex items-center text-sm font-bold text-gray-600"
                                            @click.stop="sort(heading)"
                                        >
                                            <slot
                                                :heading="heading"
                                                :name="
                                                    'heading.' + heading.value
                                                "
                                                ><span>
                                                    {{ heading.label }}
                                                </span>
                                            </slot>
                                            <span
                                                v-if="
                                                    options.sortBy ===
                                                        heading.value ||
                                                    options.sortBy ===
                                                        heading.sort_column
                                                "
                                                class="text-gray-400"
                                            >
                                                <material-icon :size="2">{{
                                                    options.sortDesc
                                                        ? "expand_less"
                                                        : "expand_more"
                                                }}</material-icon>
                                            </span>
                                        </span>
                                        <span class="text-sm">
                                            <slot
                                                :heading="heading"
                                                :index="index"
                                                :item="item"
                                                :name="'item.' + heading.value"
                                            >
                                                <span v-if="heading.html">
                                                    <span
                                                        v-html="
                                                            item[heading.value]
                                                        "
                                                    />
                                                </span>
                                                <span
                                                    v-else-if="
                                                        heading.data_accessor
                                                    "
                                                >
                                                    {{
                                                        heading.data_accessor(
                                                            item,
                                                        )
                                                    }}
                                                </span>
                                                <span v-else>
                                                    {{ item[heading.value] }}
                                                </span>
                                            </slot>
                                        </span>
                                    </div>

                                    <slot
                                        :index="index"
                                        :item="item"
                                        :name="'item.extend'"
                                    />
                                    <slot
                                        :index="index"
                                        :item="item"
                                        name="item.extend-row"
                                    />
                                </div>

                                <div
                                    v-if="!itemsPaginated.length"
                                    class="px-3 py-2 text-center"
                                >
                                    <slot name="empty">
                                        <span class="text-gray-500"
                                            ><template
                                                v-if="
                                                    searchField &&
                                                    searchField.length
                                                "
                                            >
                                                No results found for
                                                <span class="text-gray-700"
                                                    >"{{ searchField }}"</span
                                                >
                                            </template>
                                            <template v-else>
                                                {{
                                                    loading
                                                        ? loadingText
                                                        : (noDataText ??
                                                          useTranslate(
                                                              "commons",
                                                              "no_data_found",
                                                          ))
                                                }}
                                            </template>
                                        </span>
                                    </slot>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>

                <div class="mt-2">
                    <slot name="bottom-upper" />
                </div>
                <slot name="footer">
                    <div
                        v-if="!hideDefaultFooter"
                        class="flex items-center justify-end space-x-1 rtl:space-x-reverse"
                    >
                        <div class="block">
                            <span
                                class="mx-1 text-sm capitalize text-gray-500"
                                >{{ useTranslate("commons", "per_page") }}</span
                            >
                            <Listbox
                                v-model="itemsPerPage"
                                as="div"
                                class="relative inline-block text-left"
                            >
                                <ListboxButton
                                    class="mx-1 select-none rounded-md bg-white px-3 text-sm text-gray-500 outline-hidden ring-1 ring-gray-500"
                                >
                                    {{ itemsPerPage }}
                                </ListboxButton>
                                <ListboxOptions
                                    class="absolute bottom-6 right-0 w-fit origin-top-right rounded-md bg-white px-3 py-3 shadow-lg ring-1 ring-black/5 focus:outline-hidden"
                                >
                                    <ListboxOption
                                        v-for="(
                                            per_page_item, index
                                        ) in per_page_items"
                                        :key="index"
                                        #default="{ active }"
                                        :value="per_page_item"
                                    >
                                        <button
                                            :class="[
                                                active
                                                    ? ' text-primary-500'
                                                    : 'text-gray-900',
                                                'group flex w-full items-center rounded-md px-2 py-1 text-sm',
                                            ]"
                                            :disabled="
                                                per_page_item === itemsPerPage
                                            "
                                            class="capitalize disabled:text-gray-100"
                                        >
                                            {{ per_page_item }}
                                        </button>
                                    </ListboxOption>
                                </ListboxOptions>
                            </Listbox>
                        </div>
                        <span class="mx-1 select-none text-xs text-gray-500"
                            >{{ startItem }}-{{
                                itemsFiltered.length < endItem
                                    ? itemsFiltered.length
                                    : endItem
                            }}
                            of {{ itemsFiltered.length }}</span
                        >

                        <button
                            :disabled="page === 1"
                            class="aspect-square h-10 select-none rounded-full text-gray-500 ring-gray-500/50 transition-all hover:bg-gray-200 focus:outline-hidden focus:ring-1 disabled:opacity-50 disabled:hover:bg-transparent"
                            @click="previousPage"
                        >
                            <span
                                class="flex items-center justify-center rtl:rotate-180"
                                ><material-icon :size="2"
                                    >chevron_left</material-icon
                                ></span
                            >
                        </button>
                        <button
                            :disabled="page >= maxPages"
                            class="aspect-square h-10 select-none rounded-full text-gray-500 ring-gray-500/50 transition-all hover:bg-gray-200 focus:outline-hidden focus:ring-1 disabled:opacity-50 disabled:hover:bg-transparent"
                            @click="nextPage"
                        >
                            <span
                                class="flex items-center justify-center rtl:rotate-180"
                                ><material-icon :size="2"
                                    >chevron_right</material-icon
                                ></span
                            >
                        </button>
                    </div>
                </slot>
                <div class="mt-2">
                    <slot name="bottom" />
                </div>
            </div>
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
import { computed, inject, ref, toRefs, watch, watchEffect } from "vue";
import { useTranslate } from "@/Composables/Translate.js";
import SafePopper from "@/Framework/SafePopper.vue";
import MaterialIcon from "@/Framework/Icon/MaterialIcon.vue";
import FilterToggle from "@/Components/FilterToggle.vue";

defineOptions({
    name: "DataTable",
});

const props = defineProps({
    headers: {
        type: Array,
        default: () => [],
        required: true,
    },
    hiddenCols: {
        type: Array,
        default: () => [],
        required: false,
    },
    hideDefaultHeader: {
        type: Boolean,
        default: false,
        required: false,
    },
    items: {
        type: Array,
        default: () => [],
        required: true,
    },
    perPage: {
        type: Number,
        default: 5,
        required: false,
    },
    search: {
        type: String,
        default: null,
        required: false,
    },
    sortBy: {
        type: Object,
        default: () => ({
            sortBy: null,
            sortDesc: null,
        }),
        required: false,
    },
    noDataText: {
        type: String,
        default: null,
        required: false,
    },
    loadingText: {
        type: String,
        default: "Loading Data....",
        required: false,
    },
    hideDefaultFooter: {
        type: Boolean,
        default: false,
        required: false,
    },
    disablePagination: {
        type: Boolean,
        default: false,
        required: false,
    },
    canHideFilters: {
        type: Boolean,
        default: false,
        required: false,
    },
    canHideColumns: {
        type: Boolean,
        default: false,
        required: false,
    },
    backendHideColumns: {
        type: Boolean,
        default: false,
        required: false,
    },
    canDownloadCsv: {
        type: Boolean,
        default: false,
        required: false,
    },
    loading: {
        type: Boolean,
        default: false,
        required: false,
    },
    perPageItems: {
        type: Array,
        default: () => [5, 10, 15, 20, 30],
        required: false,
    },
    csvUrl: {
        type: String,
        default: "",
        required: false,
    },
    onRowItemClick: {
        type: Function,
        default: null,
        required: false,
    },
    additionalQueryParams: {
        type: Object,
        default: () => ({}),
        required: false,
    },
    dense: {
        type: Boolean,
        default: false,
        required: false,
    },
    mode: {
        type: String,
        default: "responsive",
        validator: function (value) {
            return ["responsive", "desktop", "mobile"].includes(value);
        },
    },
    rowStyleCallback: {
        type: Function,
        default: null,
        required: false,
    },
    hideDefaultHeaderInMobile: {
        type: Boolean,
        default: false,
        required: false,
    },
    hideDefaultHeaderInLarge: {
        type: Boolean,
        default: false,
        required: false,
    },
    wrapperStyles: {
        type: String,
        default: "",
        required: false,
    },
});
const emit = defineEmits([
    "update:options",
    "onChangeSort",
    "onColumnHide",
    "onColumnShow",
]);
const items = toRefs(props).items;
const itemsPerPage = ref(props.perPage);
const timeout = ref(4);
const startTimer = ref(false);
const searchField = ref(props.search);
const page = ref(1);
const options = ref({
    sortBy: props.sortBy.sortBy,
    sortDesc: props.sortBy.sortDesc,
});
const per_page_items = toRefs(props).perPageItems;
const visibleColumns = ref(getVisibleColumns());
const intervalId = ref(null);

const breakpoints = inject("responsive");
function startTimeOut() {
    if (intervalId.value) {
        clearInterval(intervalId.value);
        intervalId.value = null;
    }

    intervalId.value = setInterval(() => {
        if (startTimer.value) {
            if (timeout.value > 0) {
                timeout.value--;
            } else {
                const headers = props.headers.map((header) => header.value);
                const hiddenColumns = headers.filter(
                    (header) => !visibleColumns.value.includes(header),
                );
                const initialHiddenColumns = props.hiddenCols;
                if (
                    !(
                        hiddenColumns.length === initialHiddenColumns.length &&
                        hiddenColumns.every(
                            (value, index) =>
                                value === initialHiddenColumns[index],
                        )
                    )
                ) {
                    startTimer.value = false;

                    clearInterval(intervalId.value);
                    intervalId.value = null;
                    emit("onColumnHide", hiddenColumns);
                }
            }
        }
    }, 1000);
}

watch(
    () => visibleColumns.value,
    () => {
        emit("onColumnShow", visibleColumns.value);
    },
);

const headersVisible = computed(() => {
    if (!props.canHideColumns && !props.backendHideColumns) {
        return props.headers;
    }

    return props.headers.filter((header) => {
        return visibleColumns.value.includes(header.value);
    });
});

function updateHiddenColumns() {
    timeout.value = 4;
    startTimer.value = true;
    startTimeOut();
}

function onRowClick(item, index) {
    if (!props.onRowItemClick) return;
    props.onRowItemClick(item, index);
}

function getVisibleColumns() {
    const headers = props.headers.map((header) => header.value);
    return headers.filter((header) => !props.hiddenCols.includes(header));
}

watch(
    () => props.search,
    (newValue) => {
        searchField.value = newValue;
        page.value = 1;
    },
);

watch(
    () => props.sortBy,
    (newValue) => {
        options.value.sortBy = newValue.sortBy;
        options.value.sortDesc = newValue.sortDesc;
    },
);

const startItem = computed(() => {
    return (page.value - 1) * itemsPerPage.value;
});

const endItem = computed(() => {
    return startItem.value + itemsPerPage.value;
});
const maxPages = computed(() => {
    return Math.ceil(itemsFiltered.value.length / itemsPerPage.value);
});
const itemsPaginated = ref([]);
const additionalQueryParams = ref(props.additionalQueryParams);

const sort = (heading) => {
    let sortColumnValue = heading.value;
    if (heading.sort_column) {
        sortColumnValue = heading.sort_column;
    }

    if (!heading.sortable) return;

    if (options.value.sortBy === sortColumnValue) {
        if (options.value.sortDesc) {
            options.value.sortBy = null;
            options.value.sortDesc = false;

            emit("onChangeSort", {
                sortBy: null,
                sortDesc: false,
                ...additionalQueryParams.value,
            });

            return;
        }

        options.value.sortDesc = !options.value.sortDesc;
    } else {
        options.value.sortBy = sortColumnValue;
        options.value.sortDesc = false;
    }
    emit("onChangeSort", {
        sortBy: options.value.sortBy,
        sortDesc: options.value.sortDesc,
        ...additionalQueryParams.value,
    });
};

const itemsSorted = computed(() => {
    if (options.value.sortBy && !props.disablePagination) {
        const copy = JSON.parse(JSON.stringify(items.value));

        return copy.sort((a, b) => {
            if (options.value.sortDesc) {
                return b[options.value.sortBy] > a[options.value.sortBy]
                    ? 1
                    : -1;
            } else {
                return a[options.value.sortBy] > b[options.value.sortBy]
                    ? 1
                    : -1;
            }
        });
    } else {
        return items.value;
    }
});

const itemsFiltered = computed(() => {
    if (searchField.value && !props.disablePagination) {
        const copy = JSON.parse(JSON.stringify(itemsSorted.value));
        return copy.filter((item) => {
            return Object.keys(item).some((key) => {
                return item[key]
                    .toString()
                    .toLowerCase()
                    .includes(searchField.value.toLowerCase());
            });
        });
    } else {
        return itemsSorted.value;
    }
});

watchEffect(() => {
    if (props.disablePagination) {
        options.value.sortBy = props.sortBy.sortBy;
        options.value.sortDesc = props.sortBy.sortDesc;
    }
    if (props.disablePagination) {
        itemsPaginated.value = itemsFiltered.value;
    } else {
        itemsPaginated.value = itemsFiltered.value.slice(
            startItem.value,
            endItem.value,
        );
    }
    additionalQueryParams.value = props.additionalQueryParams;
});

const nextPage = () => {
    page.value++;
};
const previousPage = () => {
    if (page.value === 1) return;
    page.value--;
};
</script>

<style scoped></style>
