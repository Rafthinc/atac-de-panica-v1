<template>
  <div class="flex-1 flex flex-col items-center justify-center p-6 bg-gradient-to-b from-sky-50 to-white dark:from-sky-950 dark:to-slate-950">
    <div class="max-w-md w-full flex flex-col items-center space-y-12">
      <!-- Titlu sau salut blând -->
      <div class="text-center space-y-2">
        <h1 class="text-3xl font-medium text-slate-800 dark:text-slate-100">
          Bună, sunt aici pentru tine.
        </h1>
        <p class="text-lg text-slate-500 dark:text-slate-400">
          Ești într-un spațiu sigur.
        </p>
      </div>

      <!-- Butonul de intervenție (Fitts's Law: uriaș, ușor de apăsat) -->
      <UButton
        to="/sos"
        color="primary"
        size="xl"
        class="w-64 h-64 rounded-full flex flex-col items-center justify-center shadow-xl hover:shadow-2xl transition-all duration-300 hover:scale-105 active:scale-95 bg-sky-500 hover:bg-sky-600 text-white"
        @click="trackEvent('sos_button_click')"
      >
        <div class="flex flex-col items-center gap-4">
          <UIcon
            name="i-lucide-heart-handshake"
            class="w-16 h-16"
          />
          <div class="text-center">
            <span class="text-2xl font-bold uppercase tracking-wider">Ajută-mă<br>Acum</span>
          </div>
        </div>
      </UButton>

      <!-- Link secundar pentru prevenție -->
      <div class="pt-8">
        <UButton
          to="/invata"
          variant="soft"
          color="gray"
          size="lg"
          trailing-icon="i-lucide-arrow-right"
          class="rounded-full px-6 py-2 shadow-sm hover:shadow transition-all hover:-translate-y-0.5 text-slate-600 dark:text-slate-300 font-medium"
          @click="trackEvent('learn_button_click')"
        >
          Poți preveni. Învață cum!
        </UButton>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
const trackEvent = (eventName: string) => {
  // Dacă ai instalat Plausible prin scriptul lor clasic (snippet adăugat în head):
  if (typeof window !== 'undefined' && (window as any).plausible) {
    (window as any).plausible(eventName)
  }
}
</script>
