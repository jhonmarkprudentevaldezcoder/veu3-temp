<script setup>
import ChatBot from '@/components/chatbot/ChatBot.vue'
</script>

<template>

    <footer
        class="flex flex-col flex-shrink-0 w-full gap-2 px-6 py-4 lg:flex-row lg:justify-between"
    >
        <p class="text-sm text-gray-500 dark:text-gray-400">
            &#169;
            <span>{{ new Date().getFullYear() }}</span> Department of Social Welfare and Development, All rights reserved
        </p>
          <!-- ai chat bot -->
          <ChatBot/>
    </footer>
</template>
