<script setup lang="ts">
useHead({
  meta: [
    { name: 'viewport', content: 'width=device-width, initial-scale=1' }
  ],
  link: [
    { rel: 'icon', href: '/favicon.ico' }
  ],
  htmlAttrs: {
    lang: 'ro'
  }
})

useSeoMeta({
  title: 'CalmApp - Asistență Atac de Panică',
  description: 'O aplicație pentru intervenție și prevenție în atacurile de panică.'
})
</script>

<template>
  <UApp>
    <UMain class="min-h-screen bg-slate-50 dark:bg-slate-950 flex flex-col transition-colors duration-300">
      <!-- Container central care găzduiește rutele aplicației -->
      <NuxtPage />
    </UMain>
  </UApp>
</template>

<style>
/* Fonturi mai blânde și tranziții globale */
body {
  font-family: 'Inter', sans-serif;
  -webkit-font-smoothing: antialiased;
}
</style>
