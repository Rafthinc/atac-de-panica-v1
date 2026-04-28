<script setup lang="ts">
useHead({
  meta: [
    { name: 'viewport', content: 'width=device-width, initial-scale=1' }
  ],
  link: [
    { rel: 'icon', href: '/favicon.ico' }
  ],
  htmlAttrs: {
    lang: 'en'
  }
})

useSeoMeta({
  title: 'CalmApp - Panic Attack Assistance',
  description: 'An application for panic attack intervention and prevention.'
})
</script>

<template>
  <UApp>
    <UMain class="min-h-screen bg-slate-50 dark:bg-slate-950 flex flex-col transition-colors duration-300">
      <!-- Central container hosting the application routes -->
      <NuxtPage />
    </UMain>
  </UApp>
</template>

<style>
/* Softer fonts and global transitions */
body {
  font-family: 'Inter', sans-serif;
  -webkit-font-smoothing: antialiased;
}
</style>
