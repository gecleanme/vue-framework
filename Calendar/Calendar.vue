<template>
    <div>
        <div
            class="relative grid grid-cols-1"
            :key="
                JSON.stringify({
                    selectionColor,
                    highlightedDates,
                    canSelect,
                    selectAsRange,
                    multiSelect,
                })
            "
        >
            <section class="text-center">
                <div class="flex items-center justify-between">
                    <button
                        type="button"
                        class="flex items-center justify-center text-gray-400 hover:text-gray-500"
                        @click="() => displayMonth.subtract(1, 'months')"
                        dusk="calendar-prev-month-button"
                    >
                        <span class="sr-only">Previous month</span>
                        <material-icon class="rtl:rotate-180">
                            chevron_left
                        </material-icon>
                    </button>
                    <h2 class="text-xl font-semibold text-gray-900">
                        {{ displayDays.name }}
                    </h2>
                    <div class="flex items-center gap-3">
                        <button
                            type="button"
                            class="flex items-center justify-center text-gray-400 hover:text-gray-500"
                            @click="() => displayMonth.add(1, 'months')"
                            dusk="calendar-next-month-button"
                        >
                            <span class="sr-only">Previous month</span>
                            <material-icon class="rtl:rotate-180">
                                chevron_right
                            </material-icon>
                        </button>
                    </div>
                </div>
                <div
                    class="mt-6 grid grid-cols-7 bg-white text-xs font-black leading-6 text-gray-500"
                >
                    <div>S</div>
                    <div>M</div>
                    <div>T</div>
                    <div>W</div>
                    <div>T</div>
                    <div>F</div>
                    <div>S</div>
                </div>
                <div
                    class="isolate mt-2 grid grid-cols-7 gap-px rounded-lg text-sm shadow-sm ring-1 ring-gray-200"
                    :class="[calendarStyle]"
                >
                    <button
                        v-for="(day, dayIdx) in displayDays.days"
                        :key="day.date"
                        type="button"
                        :class="[
                            dayIdx === 0 && 'rounded-tl-lg',
                            dayIdx === 6 && 'rounded-tr-lg',
                            dayIdx === displayDays.days.length - 7 &&
                                'rounded-bl-lg',
                            dayIdx === displayDays.days.length - 1 &&
                                'rounded-br-lg',
                            'relative py-1.5 ',
                            day.isToday && 'border-b-2 ',
                            selectedContainerStyle(day),
                        ]"
                        @click="() => selectDate(day.date)"
                        :dusk="`calendar-date-${day.date.format('YYYY-MM-DD')}`"
                    >
                        <time
                            :datetime="day.date"
                            :dusk="`calendar-date-time-${day.date.format('YYYY-MM-DD')}`"
                            :class="[
                                'mx-auto flex h-7 w-7 items-center justify-center rounded-full',
                                dateStyle(day.date),
                            ]"
                        >
                            {{ day.date.format("DD") }}
                        </time>
                    </button>
                </div>
            </section>
        </div>
    </div>
</template>

<script setup>
import MaterialIcon from "@/Framework/Icon/MaterialIcon.vue";
import _moment from "moment-timezone";
import { computed, ref, watch } from "vue";
import { useUserTodayInstance } from "@/Composables/toUserTimezone";
import {
    eventStyles,
    selectionStyle,
} from "@/Framework/Calendar/CalendarConstants";

const props = defineProps({
    selectionColor: {
        type: String,
        default: "primary",
    },
    calendarStyle: {
        type: String,
        default: "",
    },
    highlightedDates: {
        type: Array,
        default: () => [],
    },
    range: {
        type: Object,
        default: () => ({}),
    },
    month: {
        type: String,
        default: null,
    },
    canSelect: {
        type: Boolean,
        default: false,
    },
    selectAsRange: {
        type: Boolean,
        default: false,
    },
    multiSelect: {
        type: Boolean,
        default: false,
    },
});

const emit = defineEmits([
    "update:range",
    "onClickHighlightedDate",
    "update:month",
]);

const moment = _moment;

const today = useUserTodayInstance();
const displayMonth = ref(useUserTodayInstance().startOf("month"));
const dateSelectRange = ref({
    startDate: null,
    endDate: null,
});
const selectedDates = ref([]);
const focusedDate = ref(null);

const displayDays = computed(() => {
    let days = [];

    let monthStart = displayMonth.value.clone().startOf("month");
    let monthEnd = displayMonth.value.clone().endOf("month");

    if (monthStart.day() !== 0) {
        let prevMonthEnd = monthStart.clone().subtract(1, "days");

        for (
            let lastMonthDays = prevMonthEnd.day();
            lastMonthDays >= 0;
            lastMonthDays--
        ) {
            const date = prevMonthEnd
                .clone()
                .subtract(lastMonthDays, "days")
                .format("YYYY-MM-DD");
            const newDate = moment(date);
            days.unshift({
                date: newDate,
                isCurrentMonth: false,
                isToday: today.isSame(newDate, "date"),
            });
        }
        days = days.reverse();
    }

    for (
        let currDate = monthStart.clone();
        currDate.isSameOrBefore(monthEnd);
        currDate.add(1, "days")
    ) {
        const date = currDate.format("YYYY-MM-DD");
        const newDate = moment(date);
        days.push({
            date: newDate,
            isCurrentMonth: displayMonth.value.isSame(newDate, "month"),
            isToday: today.isSame(newDate, "date"),
        });
    }

    let nextMonthStart = monthEnd.clone().add(1, "days");

    for (
        let nextMonthDays = 0;
        nextMonthDays < 6 - monthEnd.day();
        nextMonthDays++
    ) {
        const date = nextMonthStart
            .clone()
            .add(nextMonthDays, "days")
            .format("YYYY-MM-DD");
        const newDate = moment(date);
        days.push({
            date: newDate,
            isCurrentMonth: displayMonth.value.isSame(newDate, "month"),
            isToday: today.isSame(newDate, "date"),
        });
    }

    return {
        name: monthStart.format("MMMM"),
        days: days,
    };
});

const isDayInSelectedRange = (momentDate) => {
    let startDate = dateSelectRange.value.startDate;
    if (!startDate) {
        return false;
    }
    startDate = moment.min(
        dateSelectRange.value.startDate,
        dateSelectRange.value.endDate ?? dateSelectRange.value.startDate,
    );
    let endDate = moment.max(
        dateSelectRange.value.startDate,
        dateSelectRange.value.endDate ?? dateSelectRange.value.startDate,
    );
    return momentDate.isBetween(startDate, endDate, null, "[]");
};

const selectionStartDay = (momentDate) => {
    let startDate = dateSelectRange.value.startDate;
    if (!startDate) {
        return false;
    }
    startDate = moment.min(
        dateSelectRange.value.startDate,
        dateSelectRange.value.endDate ?? dateSelectRange.value.startDate,
    );
    return startDate.isSame(momentDate, "date");
};
const selectionEndDay = (momentDate) => {
    let startDate = dateSelectRange.value.startDate;
    if (!startDate) {
        return false;
    }
    startDate = moment.max(
        dateSelectRange.value.startDate,
        dateSelectRange.value.endDate ?? dateSelectRange.value.startDate,
    );
    return startDate.isSame(momentDate, "date");
};

const isHighlightedDay = (stringDate) => {
    return !!props.highlightedDates.find((evt) => evt.date === stringDate);
};

const isCurrentMonthsDate = (momentDate) => {
    return (
        displayMonth.value.format("YYYY-MM") === momentDate.format("YYYY-MM")
    );
};
const dateStyle = (momentDate) => {
    let formattedDate = momentDate.format("YYYY-MM-DD");
    let currentMonthsDate = isCurrentMonthsDate(momentDate);
    if (isHighlightedDay(formattedDate)) {
        if (isDayInSelectedRange(momentDate) || isDateSelected(formattedDate)) {
            return eventStyles["outlined-" + props.selectionColor];
        }

        const filStyle =
            focusedDate.value?.format("YYYY-MM-DD") === formattedDate &&
            !props.canSelect
                ? "filled"
                : "outlined";
        return eventStyles[
            filStyle + (currentMonthsDate ? "-primary" : "-gray")
        ];
    }
    return "";
};

const selectSingleDate = (momentDate) => {
    let formattedDate = momentDate.format("YYYY-MM-DD");
    if (props.multiSelect) {
        let indexOf = selectedDates.value.indexOf(formattedDate);
        if (indexOf > -1) {
            selectedDates.value.splice(indexOf, 1);
        } else {
            selectedDates.value.push(formattedDate);
        }
    } else {
        selectedDates.value = [formattedDate];
    }
};

function getHighlightedDataForDate(stringDate) {
    return (
        props.highlightedDates.filter((evt) => evt.date === stringDate) ?? []
    );
}

function resetSelection() {
    dateSelectRange.value = {
        startDate: null,
        endDate: null,
    };
    selectedDates.value = [];
}
const isDateSelected = (stringDate) => {
    return selectedDates.value.indexOf(stringDate) > -1;
};
function selectedContainerStyle(day) {
    let formattedDate = day.date.format("YYYY-MM-DD");
    const defaultStyle = day.isCurrentMonth
        ? " text-gray-900"
        : "bg-white/50 text-gray-400";
    const is_selected_range = isDayInSelectedRange(day.date);
    const is_selected = isDateSelected(formattedDate);
    if (!(is_selected || is_selected_range)) {
        return defaultStyle;
    }

    let is_start = selectionStartDay(day.date) || is_selected;
    let is_end = selectionEndDay(day.date) || is_selected;

    const startStyle = is_start ? " ltr:rounded-l-xl rtl:rounded-r-xl " : "";
    const endStyle = is_end ? " ltr:rounded-r-xl rtl:rounded-l-xl " : "";

    return is_selected_range || is_selected
        ? selectionStyle[props.selectionColor] + startStyle + endStyle
        : defaultStyle;
}

function selectDate(momentDate) {
    let formattedDate = momentDate.format("YYYY-MM-DD");
    if (!props.canSelect) {
        focusedDate.value = momentDate;
        if (isHighlightedDay(formattedDate)) {
            emit("onClickHighlightedDate", {
                date: formattedDate,
                events: getHighlightedDataForDate(formattedDate),
            });
        }

        return;
    }
    if (!props.selectAsRange) {
        selectSingleDate(momentDate);
        return;
    }
    if (dateSelectRange.value.startDate && dateSelectRange.value.endDate) {
        dateSelectRange.value = {
            startDate: momentDate,
            endDate: null,
        };
    } else if (dateSelectRange.value.startDate) {
        dateSelectRange.value.endDate = momentDate;
    } else {
        dateSelectRange.value = {
            startDate: momentDate,
            endDate: null,
        };
    }
}

const getOrderedDateRangeAndFormatted = () => {
    let startDate = dateSelectRange.value.startDate;
    if (!startDate) {
        return dateSelectRange.value;
    }
    startDate = moment.min(
        dateSelectRange.value.startDate,
        dateSelectRange.value.endDate ?? dateSelectRange.value.startDate,
    );
    let endDate = dateSelectRange.value.endDate
        ? moment.max(
              dateSelectRange.value.startDate,
              dateSelectRange.value.endDate,
          )
        : null;

    return {
        startDate: startDate?.format("YYYY-MM-DD"),
        endDate: endDate?.format("YYYY-MM-DD"),
    };
};

watch(() => props.selectAsRange, resetSelection);
watch(() => props.canSelect, resetSelection);
watch(
    () => JSON.stringify(displayMonth.value),
    () => emit("update:month", displayMonth.value.format("YYYY-MM-DD")),
    {
        immediate: true,
    },
);
watch(
    () => JSON.stringify(dateSelectRange.value),
    () => emit("update:range", getOrderedDateRangeAndFormatted()),
);

defineExpose({ resetSelection });
</script>
