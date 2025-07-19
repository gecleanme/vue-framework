<template>
    <text-field :type="showPassword ? 'text' : 'password'">
        <template #append-icon>
            <div class="cursor-pointer" @click="toggleShowPassword">
                <EyeIcon v-if="!showPassword" class="h-6 w-6" />
                <EyeOffIcon v-else class="h-6 w-6" />
            </div>
        </template>
    </text-field>
</template>

<script setup>
import { ref } from "vue";
import TextField from "@/Framework/TextField/TextField.vue";
import { EyeIcon, EyeOffIcon } from "@heroicons/vue/outline";

defineOptions({
    name: "PasswordField",
});

const showPassword = ref(false);

function toggleShowPassword() {
    showPassword.value = !showPassword.value;
}
</script>
