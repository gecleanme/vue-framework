<template>
    <div>
        <transition name="slide" mode="out-in">
            <slot :items="pageItems" :page="currentPage" :key="currentPage" />
        </transition>

        <div
            class="flex justify-center gap-3 pt-3"
            v-if="items?.length > perPage"
        >
            <slot name="prevPage" :page="currentPage" v-if="currentPage > 1">
                <primary-button
                    @click="changePage('prev')"
                    class="aspect-square"
                    rounded-style="rounded-full"
                >
                    <material-icon
                        :rotate="
                            $page.props.locale === 'en' ? 'default' : 'down'
                        "
                    >
                        chevron_left
                    </material-icon>
                </primary-button>
            </slot>

            <slot
                name="nextPage"
                :page="currentPage"
                v-if="currentPage < totalPages"
            >
                <primary-button
                    @click="changePage('next')"
                    class="aspect-square"
                    rounded-style="rounded-full"
                >
                    <material-icon
                        :rotate="
                            $page.props.locale === 'en' ? 'default' : 'down'
                        "
                    >
                        chevron_right
                    </material-icon>
                </primary-button>
            </slot>
        </div>
    </div>
</template>

<script setup>
import { computed, ref, watch } from "vue";
import PrimaryButton from "@/Framework/Button/PrimaryButton.vue";
import MaterialIcon from "@/Framework/Icon/MaterialIcon.vue";

defineOptions({
    name: "ItemIterator",
});

const props = defineProps({
    items: {
        type: Array,
        required: true,
    },
    perPage: {
        type: Number,
        default: 10,
    },
});

const currentPage = ref(1);
const totalPages = computed(() =>
    Math.ceil(props.items.length / props.perPage),
);
const pageItems = computed(() => {
    const startIdx = (currentPage.value - 1) * props.perPage;
    const endIdx = startIdx + props.perPage;
    return props.items.slice(startIdx, endIdx);
});

const changePage = (direction) => {
    if (direction === "next") {
        currentPage.value = Math.min(currentPage.value + 1, totalPages.value);
    } else if (direction === "prev") {
        currentPage.value = Math.max(currentPage.value - 1, 1);
    }
};

watch(
    () => props.items,
    () => {
        currentPage.value = 1;
    },
);

watch(
    () => props.perPage,
    () => {
        currentPage.value = 1;
    },
);
</script>

<style scoped>
.slide-enter-active,
.slide-leave-active {
    transition: opacity 0.5s ease-in-out;
}
.slide-enter,
.slide-leave-to {
    opacity: 0;
}
</style>
