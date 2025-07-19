<template>
    <div class="flex flex-col w-full select-none">
        <label
            v-if="label && label.length"
            :class="[
                errorMessage && errorMessage.length
                    ? 'text-red-400'
                    : 'text-gray-500',
            ]"
            class="text-md px-1 select-none"
            >{{ label }}</label
        >
        <div class="gap-3 flex w-full">
            <slot name="prepend" />
            <input
                v-model="newTagName"
                :class="[
                    {
                        'border-red-300  focus:ring-red-300 focus:border-red-400':
                            errorMessage && errorMessage.length,
                    },
                    ,
                    inputSize,
                ]"
                type="text"
                v-bind="$attrs"
                @keydown.enter="addTag"
                :placeholder="useTranslate('tag_input_group', 'placeholder')"
                :disabled="disabled"
            />
            <slot name="append" />
        </div>
        <div class="flex flex-wrap gap-2 mt-3 w-full justify-end">
            <pill v-for="tag in tags" color="primary" :key="tag">
                <template #label>
                    <div class="flex items-center gap-3">
                        <slot name="tag-prepend" :item="tag" />
                        <slot name="tag" :item="tag"> {{ tag }}</slot>

                        <confirmed-action
                            :executor="() => removeTag(tag)"
                            #default="scope"
                        >
                            <button @click="scope.confirm" v-if="!disabled">
                                <material-icon :size="1" color="none">
                                    close
                                </material-icon>
                            </button>
                        </confirmed-action>
                    </div>
                </template>
            </pill>
        </div>
        <div class="break-words select-none">
            <p
                v-if="errorMessage && errorMessage.length"
                class="text-sm text-red-400 px-1 mt-1"
            >
                {{ errorMessage }}
            </p>
        </div>
    </div>
</template>

<script setup>
import { ref, watch } from "vue";
import Pill from "@/Framework/Pill/Pill.vue";
import ConfirmedAction from "@/Layouts/components/dialogs/ConfirmedAction.vue";
import MaterialIcon from "@/Framework/Icon/MaterialIcon.vue";
import { useTranslate } from "@/Composables/Translate.js";

defineOptions({
    name: "TagInputGroup",
    inheritAttrs: false,
});

const props = defineProps({
    modelValue: { type: Array, default: () => [], required: false },
    label: { type: String, default: null, required: false },
    errorMessage: { type: String, default: null, required: false },
    inputSize: { type: String, default: "w-full", required: false },
    canDelete: { type: Boolean, default: true, required: false },
    disabled: { type: Boolean, default: false, required: false },
});

const emit = defineEmits(["update:modelValue", "change", "addTag"]);

const tags = ref(tagsFormatted(props.modelValue));
const newTagName = ref(null);

watch(
    () => props.modelValue,
    (value) => {
        tags.value = tagsFormatted(value);
    },
);

function removeTag(tag) {
    tags.value.splice(tags.value.indexOf(tag), 1);
    emit("update:modelValue", tagsFormatted());
    emit("change", tagsFormatted());
}

function addTag() {
    let tagValue = newTagName.value;
    if (!tagValue || !tagValue.trim().length) {
        return;
    }
    tagValue = tagValue.trim();
    const found = tags.value.find(
        (tag) => tag.toLowerCase() === tagValue.toLowerCase(),
    );
    if (!found) {
        tags.value.push(tagValue);
        emit("addTag", tagValue);
        newTagName.value = null;
        emit("update:modelValue", tagsFormatted());
        emit("change", tagsFormatted());
    }
}

function tagsFormatted(value = null) {
    if (value) {
        return value?.filter((tag) => tag?.trim().length);
    }
    return tags.value?.filter((tag) => tag?.trim().length);
}
</script>

<style scoped></style>
