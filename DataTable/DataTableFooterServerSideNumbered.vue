<template>
    <div
        class="flex flex-col space-y-3 lg:space-y-0 lg:flex-row items-end lg:justify-end lg:items-center"
    >
        <span class="text-sm text-gray-500">
            {{ useTranslate("commons", "showing") }} {{ from }} - {{ to }}
            {{ useTranslate("commons", "of") }} {{ total }}
        </span>
        <div class="block h-full lg:mx-3">
            <span class="text-sm text-gray-500 mx-1 capitalize">{{
                useTranslate("commons", "per_page")
            }}</span>
            <Listbox as="div" class="h-full relative inline-block text-left">
                <ListboxButton
                    class="mx-1 select-none text-sm text-gray-500 outline-hidden ring-1 lg:min-w-[3rem] px-2 py-2 rounded-md ring-primary-500"
                >
                    {{ itemsPerPage }}
                </ListboxButton>
                <ListboxOptions
                    class="absolute origin-top-right bottom-6 right-0 w-fit rounded-md bg-white shadow-lg ring-1 py-3 px-3 ring-black/5 focus:outline-hidden"
                >
                    <ListboxOption
                        v-for="(per_page_item, index) in per_page_items"
                        :key="index"
                        #default="{ active }"
                        :value="per_page_item"
                    >
                        <button
                            :class="[
                                active ? 'text-primary-500' : 'text-gray-900',
                                'group flex w-full items-center rounded-md px-2 py-1 text-sm',
                            ]"
                            :disabled="per_page_item === itemsPerPage"
                            class="capitalize disabled:text-gray-400"
                            @click="changePerPage(per_page_item)"
                        >
                            {{ per_page_item }}
                        </button>
                    </ListboxOption>
                </ListboxOptions>
            </Listbox>
        </div>

        <div class="grid grid-flow-col gap-1">
            <button
                v-for="link in linksComputed"
                :key="link"
                :class="[
                    link.active
                        ? 'bg-primary-500 text-white'
                        : 'text-primary-500',
                ]"
                :disabled="link.active || !link.url"
                class="lg:min-w-[3rem] px-2 py-2 outline-hidden ring-1 ring-primary-500 disabled:ring-0 rounded-lg flex items-center justify-center"
                @click="gotoPage(link)"
                :dusk="link.dusk"
            >
                <span v-if="link.type === 'label'"> {{ link.label }} </span>
                <template v-else>
                    <svg
                        v-if="link.label === 'last'"
                        class="h-4 w-4 text-primary-500 rtl:rotate-180"
                        fill="none"
                        stroke="currentColor"
                        stroke-width="2"
                        viewBox="0 0 24 24"
                        xmlns="http://www.w3.org/2000/svg"
                    >
                        <path
                            d="M13 5l7 7-7 7M5 5l7 7-7 7"
                            stroke-linecap="round"
                            stroke-linejoin="round"
                        />
                    </svg>

                    <svg
                        v-if="link.label === 'first'"
                        class="h-4 w-4 text-primary-500 rtl:rotate-180"
                        fill="none"
                        stroke="currentColor"
                        stroke-width="2"
                        viewBox="0 0 24 24"
                        xmlns="http://www.w3.org/2000/svg"
                    >
                        <path
                            d="M11 19l-7-7 7-7m8 14l-7-7 7-7"
                            stroke-linecap="round"
                            stroke-linejoin="round"
                        />
                    </svg>
                </template>
            </button>
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
import { useInertia } from "@/DI/index.js";
import { ref, toRefs, watchEffect } from "vue";
import { useTranslate } from "@/Composables/Translate";
import { route } from "ziggy-js";

defineOptions({
    name: "ServerSidePaginator",
});

const props = defineProps({
    links: { type: Array, default: () => [], required: false },
    total: { type: Number, default: 0, required: false },
    from: { type: Number, default: 0, required: false },
    to: { type: Number, default: 0, required: false },
    currentPage: { type: Number, default: 0, required: false },
    itemsPerPage: { type: Number, default: 10, required: false },
    perPageItems: {
        type: Array,
        default: () => [5, 10, 15, 20, 30],
        required: false,
    },
    apiPagination: { type: Boolean, default: false, required: false },
});

const emit = defineEmits(["onFetchAPI"]);

const links = toRefs(props).links ?? ref([]);
const currentPage = toRefs(props).currentPage;
const total = toRefs(props).total;
const linksComputed = ref([]);
const numPages = ref(0);
const per_page_items = toRefs(props).perPageItems;

function gotoPage(link) {
    if (!link.url) return;
    if (props.apiPagination) {
        emit("onFetchAPI", {
            itemsPerPage: props.itemsPerPage,
            page: link.page,
        });
    } else {
        useInertia().visit(link.url);
    }
}

function changePerPage(per_page_item) {
    if (props.apiPagination) {
        emit("onFetchAPI", {
            itemsPerPage: per_page_item,
            page: 1,
        });
        return;
    }
    useInertia().visit(
        route(route().current(), {
            ...route().params,
            itemsPerPage: per_page_item,
            page: 1,
        }),
    );
}

watchEffect(() => {
    numPages.value = Math.ceil(total.value / props.itemsPerPage);

    linksComputed.value = links.value.filter(
        (link) => !isNaN(link.label) || link.label == "...",
    );

    linksComputed.value = linksComputed.value.map((link) => {
        return {
            ...link,
            page: parseInt(link.label),
            type: "label",
        };
    });

    if (currentPage.value > 1) {
        linksComputed.value.unshift({
            label: "first",
            page: 1,
            type: "icon",
            url: route(route().current(), {
                ...route().params,
                page: 1,
            }),
        });
    }
    if (currentPage.value < numPages.value && numPages.value > 3) {
        linksComputed.value.push({
            label: "last",
            page: numPages.value,
            type: "icon",
            url: route(route().current(), {
                ...route().params,
                page: numPages.value,
            }),
            dusk: "last-page-button",
        });
    }
});
</script>

<style scoped></style>
