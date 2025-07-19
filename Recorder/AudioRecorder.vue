<template>
    <div class="flex p-3">
        <div
            class="fixed w-fit h-fit bottom-2 right-2"
            v-if="(isRecording || isSubmitting) && !dialog && !embedded"
        >
            <primary-button
                @click="openDialog"
                class="animate-bounce hover:animate-none w-16 h-16"
                rounded-style="rounded-full"
            >
                <material-icon> mic </material-icon>
            </primary-button>
        </div>
        <modal-dialog v-if="!embedded" v-model="dialog" :key="modalKey">
            <template #activator="{ open }">
                <RecordingIndicator
                    :volume-level="dialog ? 0 : volumeLevel"
                    :multiplier="2"
                >
                    <div
                        class="aspect-square inline-flex"
                        @click.stop="disabled ? () => {} : open(true)"
                    >
                        <slot :recording="isRecording">
                            <span
                                :class="[
                                    isRecording
                                        ? 'text-red-600 animate-pulse'
                                        : 'text-gray-500',
                                ]"
                                class="cursor-pointer"
                                ><material-icon :size="2"
                                    >mic</material-icon
                                ></span
                            >
                        </slot>
                    </div>
                </RecordingIndicator>
            </template>

            <template #default>
                <div
                    class="bg-white px-10 py-8 flex flex-col items-center space-y-3 rounded-lg"
                >
                    <button
                        v-if="!isRecording"
                        class="rounded-full ring-3 aspect-square outline-hidden focus:font-bold ring-primary-500 text-primary-500 hover:shadow-lg"
                        @click="startRecord"
                        :class="[recordButtonStyles]"
                    >
                        <slot name="record-icon">
                            <material-icon :size="7"> mic </material-icon>
                        </slot>
                    </button>
                    <div
                        v-if="!isSubmitting"
                        class="flex justify-center gap-x-3"
                    >
                        <!--            <button-->
                        <!--                v-if="isRecording"-->
                        <!--                class="rounded-full ring-3 ring-primary-400 outline-hidden focus:font-bold text-primary-400 hover:shadow-lg material-symbols-outlined text-7xl"-->
                        <!--                @click="pauseResumeRecording">-->

                        <!--              {{ isPaused ? 'play_arrow' : 'pause' }}-->
                        <!--            </button>-->
                        <RecordingIndicator
                            :volume-level="volumeLevel"
                            v-if="isRecording"
                        >
                            <button
                                class="rounded-full bg-white aspect-square ring-3 ring-red-400 border ring-offset-2 focus:font-bold text-red-400 hover:shadow-lg"
                                @click="() => stopRecording()"
                            >
                                <material-icon :size="7">mic</material-icon>
                            </button>
                            <template #label>
                                <span
                                    class="text-2xl font-semibold text-primary-400 bg bg-white/80 rounded-md"
                                >
                                    {{ useFormatSecondsToTimer(seconds) }}</span
                                >
                            </template>
                        </RecordingIndicator>
                        <!--                        <confirmed-action-->
                        <!--                            #default="{ confirm }"-->
                        <!--                            :executor="() => cancelRecording()"-->
                        <!--                        >-->
                        <!--                            <button-->
                        <!--                                v-if="isRecording"-->
                        <!--                                class="rounded-full ring-3 ring-gray-400 outline-hidden focus:font-bold text-gray-400 hover:shadow-lg material-symbols-outlined text-7xl"-->
                        <!--                                @click="confirm"-->
                        <!--                            >-->
                        <!--                                close-->
                        <!--                            </button>-->
                        <!--                        </confirmed-action>-->
                    </div>
                    <div v-else class="flex justify-center">
                        <span class="text-2xl font-semibold text-primary-400">{{
                            useTranslate("commons", "uploading")
                        }}</span>
                    </div>

                    <span
                        v-if="!isRecording"
                        class="text-2xl font-semibold text-primary-400"
                    >
                        {{ useFormatSecondsToTimer(seconds) }}</span
                    >
                </div>
            </template>
        </modal-dialog>
        <slot
            name="embedded"
            v-else
            :isRecording="isRecording"
            :isSubmitting="isSubmitting"
            :volumeLevel="volumeLevel"
            :stopRecording="stopRecording"
            :startRecord="startRecord"
            :seconds="seconds"
        >
            <div
                class="bg-white px-10 py-8 flex flex-col items-center space-y-3 rounded-lg"
            >
                <button
                    v-if="!isRecording"
                    class="rounded-full aspect-square ring-3 outline-hidden focus:font-bold ring-primary-500 text-primary-500 hover:shadow-lg"
                    @click="startRecord"
                    :class="[recordButtonStyles]"
                >
                    <slot name="record-icon">
                        <material-icon :size="7"> mic </material-icon>
                    </slot>
                </button>
                <div v-if="!isSubmitting" class="flex justify-center gap-x-3">
                    <RecordingIndicator
                        :volume-level="volumeLevel"
                        v-if="isRecording"
                    >
                        <button
                            class="rounded-full bg-white aspect-square ring-3 ring-red-400 border ring-offset-2 focus:font-bold text-red-400 hover:shadow-lg"
                            @click="() => stopRecording()"
                        >
                            <material-icon :size="7">mic</material-icon>
                        </button>

                        <template #label>
                            <span
                                class="text-2xl font-semibold text-primary-400 bg bg-white/80 rounded-md"
                            >
                                {{ useFormatSecondsToTimer(seconds) }}</span
                            >
                        </template>
                    </RecordingIndicator>
                </div>
                <div v-else class="flex justify-center">
                    <span class="text-2xl font-semibold text-primary-400">{{
                        useTranslate("commons", "uploading")
                    }}</span>
                </div>

                <span
                    v-if="!isRecording"
                    class="text-2xl font-semibold text-primary-400"
                >
                    {{ useFormatSecondsToTimer(seconds) }}</span
                >
            </div>
        </slot>
    </div>
</template>

<script setup>
import PrimaryButton from "@/Framework/Button/PrimaryButton.vue";
import { useInertia } from "@/DI/index.js";
import { onMounted, ref, watch } from "vue";
import ModalDialog from "@/Framework/ModalDialog/ModalDialog.vue";
import { useFormatSecondsToTimer } from "@/Composables/FormatSecondsToTimer.js";
import { useTranslate } from "@/Composables/Translate";
import MaterialIcon from "@/Framework/Icon/MaterialIcon.vue";
import { useLocalNotificationStore } from "@/stores/localNotification.js";
import RecordingIndicator from "@/Framework/Recorder/RecordingIndicator.vue";
import {
    convertWebmBlobToMp3Blob,
    useFfmpeg,
} from "@/Composables/ffmpeg/resolveFfmpeg.js";

defineOptions({
    name: "AudioRecorder",
});

const props = defineProps({
    post_route: { type: String, default: null, required: false },
    recordButtonStyles: { type: String, default: "", required: false },
    disabled: { type: Boolean, default: false, required: false },
    embedded: { type: Boolean, default: false, required: false },
});

const emit = defineEmits([
    "onRecorded",
    "updateIsRecording",
    "update:isSubmitting",
]);

let analyser = null;
let dataArray = null;
let mediaRecorder = null;
let audioContext = null;
let audioChunks = [];
const parts = ref([]);
const isRecording = ref(false);
const isPaused = ref(false);
const isSubmitting = ref(false);
const seconds = ref(0);
const dialog = ref(false);
const modalKey = ref(new Date());
const notificationStore = ref(useLocalNotificationStore());
const volumeLevel = ref(0);

function openDialog() {
    dialog.value = true;
    modalKey.value = new Date();
}

const disconnectAnalyzer = () => {
    if (analyser) {
        analyser.disconnect();
        analyser = null;
    }
    volumeLevel.value = 0;
};

onMounted(() => {
    useFfmpeg();

    setInterval(() => {
        if (isRecording.value && !isPaused.value) {
            seconds.value = seconds.value + 1;
        }
    }, 1000);
});

async function stopRecording(emitEvent = true, action = null) {
    try {
        disconnectAnalyzer();

        if (mediaRecorder) {
            mediaRecorder.stop();

            mediaRecorder.ondataavailable = async (event) => {
                audioChunks.push(event.data);
                const combined = await convertWebmBlobToMp3Blob(
                    new Blob(audioChunks, { type: "audio/webm" }),
                );

                const formData = new FormData();
                formData.set("recording", combined, "recording.mp3");

                if (props.post_route) {
                    isSubmitting.value = true;
                    emit("updateIsRecording", false);
                    useInertia().post(
                        props.post_route,
                        {
                            recording: formData.get("recording"),
                        },
                        {
                            preserveScroll: true,
                            onSuccess: () => {
                                dialog.value = false;
                                isSubmitting.value = false;
                                isRecording.value = false;
                                isPaused.value = false;
                                seconds.value = 0;
                            },
                            onError: (error) => {
                                isSubmitting.value = false;
                                notificationStore.value.addNotification(
                                    "error",
                                    error.message ?? error.recording,
                                );
                            },
                        },
                    );
                } else {
                    if (emitEvent) {
                        emit("onRecorded", formData.get("recording"));
                        if (action) {
                            action(formData.get("recording"));
                        }
                    } else {
                        isRecording.value = false;
                        isPaused.value = false;
                        seconds.value = 0;
                        dialog.value = false;
                        if (action) {
                            action(formData.get("recording"));
                        }
                        return formData.get("recording");
                    }
                    isRecording.value = false;
                    isPaused.value = false;
                    seconds.value = 0;
                    dialog.value = false;
                }
            };
        } else if (!emitEvent) {
            return null;
        }
    } catch (e) {
        notificationStore.value.addNotification("error", e.message);
    }
}

async function startRecord() {
    if (isRecording.value || isPaused.value) {
        return;
    }

    try {
        const stream = await navigator.mediaDevices.getUserMedia({
            audio: true,
        });
        audioChunks = [];
        mediaRecorder = new MediaRecorder(stream);
        mediaRecorder.ondataavailable = (event) => {
            audioChunks.push(event.data);
        };
        mediaRecorder.start();

        // Initialize AudioContext and AnalyserNode
        audioContext = new (window.AudioContext || window.webkitAudioContext)();
        const source = audioContext.createMediaStreamSource(stream);
        analyser = audioContext.createAnalyser();
        analyser.fftSize = 256;
        const bufferLength = analyser.frequencyBinCount;
        dataArray = new Uint8Array(bufferLength);
        source.connect(analyser);

        parts.value = [];
        seconds.value = 0;

        isRecording.value = true;
        isPaused.value = false;
        isSubmitting.value = false;
        updateVolume();
    } catch (e) {
        notificationStore.value.addNotification("error", e.message);
    }
}
function updateVolume() {
    if (!analyser || !isRecording.value) return;

    analyser.getByteFrequencyData(dataArray);
    const average =
        dataArray.reduce((acc, value) => acc + value, 0) / dataArray.length;
    volumeLevel.value = average / 255; // Normalize to 0-1 range

    if (isRecording.value) {
        requestAnimationFrame(updateVolume);
    }
}
watch(isRecording, (value) => {
    emit("updateIsRecording", value);
});

defineExpose({ stopRecording, startRecord });
</script>

<style scoped></style>
