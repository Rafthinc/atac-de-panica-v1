<template>
  <div class="flex-1 p-6 lg:p-12 bg-slate-50 dark:bg-slate-950">
    <div class="max-w-4xl mx-auto space-y-8">
      <!-- Header -->
      <div class="flex items-center gap-4">
        <UButton
          to="/"
          icon="i-lucide-arrow-left"
          variant="ghost"
          color="neutral"
          class="rounded-full"
        />
        <div>
          <h1 class="text-3xl font-semibold text-slate-800 dark:text-slate-100">Prevenție și Învățare</h1>
          <p class="text-slate-500">Înțelege ce ți se întâmplă și construiește-ți uneltele pentru calm.</p>
        </div>
      </div>

      <!-- Grid de carduri (Don Norman: Conceptual Models) -->
      <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
        <UCard v-for="(lesson, index) in lessons" :key="index" class="hover:shadow-lg transition-shadow cursor-pointer border-t-4" :style="`border-top-color: ${lesson.color}`">
          <template #header>
            <div class="flex items-center gap-3">
              <div :class="`p-2 rounded-lg bg-${lesson.colorBase}-100 dark:bg-${lesson.colorBase}-900/30 text-${lesson.colorBase}-600 dark:text-${lesson.colorBase}-400`">
                <UIcon :name="lesson.icon" class="w-6 h-6" />
              </div>
              <h3 class="text-xl font-medium text-slate-800 dark:text-slate-100">{{ lesson.title }}</h3>
            </div>
          </template>
          
          <p class="text-slate-600 dark:text-slate-400">
            {{ lesson.description }}
          </p>

          <template #footer>
            <div class="flex justify-end">
              <UButton
                v-if="lesson.to"
                :to="lesson.to"
                variant="ghost"
                :color="lesson.buttonColor as any"
                trailing-icon="i-lucide-arrow-right"
              >
                Începe lecția
              </UButton>
              <UButton
                v-else
                variant="ghost"
                color="neutral"
                trailing-icon="i-lucide-clock"
              >
                În curând...
              </UButton>
            </div>
          </template>
        </UCard>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
const lessons = [
  {
    title: 'Mecanismul Panicii',
    description: 'Află cum funcționează sistemul de alarmă al corpului tău (luptă sau fugi) și de ce este inofensiv, chiar dacă e neplăcut.',
    icon: 'i-lucide-brain-circuit',
    color: '#3b82f6', // blue-500
    colorBase: 'blue',
    buttonColor: 'primary',
    to: '/mecanismul-panicii'
  },
  {
    title: 'Exercițiul 5-4-3-2-1',
    description: 'Tehnică de grounding (ancorare) pentru a te readuce în prezent folosind cele cinci simțuri.',
    icon: 'i-lucide-eye',
    color: '#10b981', // emerald-500
    colorBase: 'emerald',
    buttonColor: 'primary',
    to: '/exercitiul-54321'
  },
  {
    title: 'Respirația în 4 Timpi',
    description: 'Învață tehnica Box Breathing folosită pentru reglarea rapidă a sistemului nervos autonom.',
    icon: 'i-lucide-wind',
    color: '#06b6d4', // cyan-500
    colorBase: 'cyan',
    buttonColor: 'primary',
    to: '/respiratia-in-4-timpi'
  },
  {
    title: 'Setări și Ancore',
    description: 'Cum să-ți pregătești dimineața și să-ți creezi "ancore" mentale pentru momentele de stres.',
    icon: 'i-lucide-anchor',
    color: '#8b5cf6', // violet-500
    colorBase: 'violet',
    buttonColor: 'violet'
  }
]
</script>