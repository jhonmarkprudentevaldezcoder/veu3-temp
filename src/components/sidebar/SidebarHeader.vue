<script setup>
import Logo from '@/components/Logo.vue'
import Button from '@/components/Button.vue'
import { Icon } from '@iconify/vue'
import { sidebarState } from '@/composables'
import logo from '@/assets/images/DSWD-Logo.png'

</script>

<template>
    <div class="flex items-center justify-between flex-shrink-0 px-3">
        <router-link
            :to="{ name: 'Dashboard' }"
            class="inline-flex items-center gap-2"
        >
           <!--  <img class="object-contain h-8"
                :src="logo"
                alt="DSWD Logo"
            /> -->
           
            <Logo aria-hidden="true" class="w-10 h-auto" />
            <span class="">DSWD</span>
        </router-link>

        <Button
            icon-only
            variant="secondary"
            v-slot="{ iconSizeClasses }"
            v-show="sidebarState.isOpen || sidebarState.isHovered"
            @click="sidebarState.isOpen = !sidebarState.isOpen"
            :sr-text="sidebarState.isOpen ? 'Close sidebar' : 'Open sidebar'"
        >
            <Icon
                icon="solar:hamburger-menu-broken"
                aria-hidden="true"
                v-show="sidebarState.isOpen"
                :class="['hidden lg:block', iconSizeClasses]"
            />

            <Icon
                icon="solar:hamburger-menu-outline"
                aria-hidden="true"
                v-show="!sidebarState.isOpen"
                :class="['hidden lg:block', iconSizeClasses]"
            />

            <Icon
                icon="mdi:window-close"
                aria-hidden="true"
                :class="['lg:hidden', iconSizeClasses]"
            />
        </Button>
    </div>
</template>
