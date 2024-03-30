<template>
    <div v-if="!showErrorAlert" class="text-white bg-blue-800 bg-opacity-50 p-2 text-sm mb-2 rounded-sm">
        <p class="font-bold">Welcome !</p>
        <p class="mt-1 text-sm text-theme-muted">
            Please sign in to access your account.
        </p>
    </div>
    <form id="login-form" autocomplete="off" @submit.prevent>
        <div class="grid gap-6">
            <!-- Email input -->
            <div class="space-y-2">
                <Label for="email" value="Email" />

                <InputIconWrapper icon="mdi:email-outline">
                    <Input withIcon id="email" type="email" name="email" class="block w-full" placeholder="Email"
                        v-model="payload.email" autofocus autocomplete="username" :id="getId('email-input')"
                        :invalid="validator.email.$invalid"
                        :invalid-text="validator.email.$invalid ? validator.email.$errors[0].$message : null" />
                </InputIconWrapper>
            </div>

            <!-- Password input -->
            <div class="space-y-2">
                <Label for="password" value="Password" />

                <InputIconWrapper icon="mdi:lock-outline">
                    <Input withIcon id="password" type="password" name="password" class="block w-full"
                        placeholder="Password" :id="getId('password-input')" v-model="payload.password"
                        autocomplete="current-password" :invalid="validator.password.$invalid"
                        :invalid-text="validator.password.$invalid ? validator.password.$errors[0].$message : null"
                        @keydown.enter.prevent="handleFormSubmit" @blur="validator.password.$touch" />
                </InputIconWrapper>
            </div>

            <!-- Remember me -->
            <div class="flex items-center justify-between">
                <label class="flex items-center">
                    <Checkbox name="remember" v-model:checked="payload.remember" />
                    <span class="ml-2 text-sm text-gray-600">Remember me</span>
                </label>

                <router-link :to="{ name: 'ForgotPassword' }" class="text-sm text-gray-200 hover:underline">
                    Forgot your password?
                </router-link>
            </div>

            <!-- Login button -->
            <div>
                <Button :id="getId('login-button')" :is-loading="isLoading" loading-text="Loading..."
                    class="justify-center w-full gap-2" @click="handleFormSubmit" :disabled="payload.processing"
                    left-icon="mdi:login">
                    <span>Log in</span>
                </Button>
            </div>

            <!-- Register link -->
            <p class="text-sm text-gray-600 dark:text-gray-400">
                Don't have an account?
                <router-link :to="{ name: 'Register' }" class="text-gray-200 hover:underline">
                    Register
                </router-link>
            </p>
        </div>
    </form>
</template>

<script setup>

import { useToast } from 'vue-toastification';
import InputIconWrapper from '@/components/InputIconWrapper.vue'
import Label from '@/components/Label.vue'
import Input from '@/components/Input.vue'
import Checkbox from '@/components/Checkbox.vue'
import Button from '@/components/Button.vue'
import { useRoute, useRouter } from 'vue-router'
import { usePrependOrAppendOnce } from '@/composables/helpers.js'
import { required, helpers } from '@vuelidate/validators'
import { useVuelidate } from '@vuelidate/core'
import { reactive, ref } from 'vue'

const getId = usePrependOrAppendOnce('components-authentication-page-login-form')
const route = useRoute()
const toast = useToast();

const payload = reactive({
    email: route.query.email || '',
    password: '',
})

// Handle form validation
const formRules = {
    $lazy: true,
    email: {
        required: helpers.withMessage('Email address required', required),
    },
    password: {
        required: helpers.withMessage('Please enter your password', required),
    },
}

const isLoading = ref(false)
const validator = useVuelidate(formRules, payload)

// Handle form submission
const showErrorAlert = ref(false)
const router = useRouter()

const handleFormSubmit = async () => {
    const valid = await validator.value.$validate()
    if (!valid) return

    isLoading.value = true
    const body = payload;
    const auth = useAuthStore()
    const response = await auth.login(body)
    isLoading.value = false

    if (response.success) {
        // Handle route redirection from account verification page
        if (route.query.from_account_verification) {
            return await router.replace({
                name: 'verify-account',
                params: {
                    id: route.query.id,
                    hash: route.query.hash,
                },
                query: {
                    expires: route.query.expires,
                    signature: route.query.signature,
                },
            })
        }

        if (auth.authenticatedUser.email_verified_at) return await router.replace({ name: 'home' })
        else return await router.replace({ name: 'verify-email-guard' })
    }
    toast.error('Error! This is an error message.', {
        position: 'top-center',
        timeout: 2000
    });
    showErrorAlert.value = true
}

</script>