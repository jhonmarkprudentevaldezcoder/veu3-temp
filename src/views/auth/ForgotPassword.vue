<script setup>
import { reactive } from 'vue'
import InputIconWrapper from '@/components/InputIconWrapper.vue'
import Button from '@/components/Button.vue'
import Input from '@/components/Input.vue'
import Label from '@/components/Label.vue'

const forgotPasswordForm = reactive({
    email: '',
    processing: false,
})

const submit = () => {
    //
}
</script>

<template>
    <div class="mb-4 text-sm text-gray-600 dark:text-gray-400">
        Lost your password? Don't panic! Just enter your email, and we'll send you a link to reset it.
    </div>

    <form @submit.prevent="submit">
        <div class="grid gap-6">
            <!-- Email input -->
            <div class="space-y-2">
                <Label for="email" value="Email" />

                <InputIconWrapper icon="mdi:email-outline">
                    <Input
                        withIcon
                        id="email"
                        type="email"
                        class="block w-full"
                        placeholder="Email"
                        v-model="forgotPasswordForm.email"
                        required
                        autofocus
                        autocomplete="username"
                    />
                </InputIconWrapper>
            </div>

            <!-- Submit button -->
            <div>
                <Button
                    type="submit"
                    class="justify-center w-full gap-2"
                    :disabled="forgotPasswordForm.processing"
                    left-icon="mdi:send-outline"
                >
                    <span>Email Password Reset Link</span>
                </Button>
            </div>
        </div>
    </form>
</template>
