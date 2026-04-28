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
          <h1 class="text-3xl font-semibold text-slate-800 dark:text-slate-100">
            Prevention and Learning
          </h1>
          <p class="text-slate-500">
            Understand what is happening to you and build your tools for calm.
          </p>
        </div>
      </div>

      <!-- Card grid (Don Norman: Conceptual Models) -->
      <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
        <UCard
          v-for="(lesson, index) in lessons"
          :key="index"
          class="hover:shadow-lg transition-shadow cursor-pointer border-t-4 focus:outline-none focus:ring-2 focus:ring-blue-500"
          :style="`border-top-color: ${lesson.color}`"
          @click="navigateTo(lesson.path)"
        >
          <template #header>
            <div class="flex items-center gap-3">
              <div :class="`p-2 rounded-lg bg-${lesson.colorBase}-100 dark:bg-${lesson.colorBase}-900/30 text-${lesson.colorBase}-600 dark:text-${lesson.colorBase}-400`">
                <UIcon
                  :name="lesson.icon"
                  class="w-6 h-6"
                />
              </div>
              <h3 class="text-xl font-medium text-slate-800 dark:text-slate-100">
                {{ lesson.title }}
              </h3>
            </div>
          </template>
          <p class="text-slate-600 dark:text-slate-400">
            {{ lesson.description }}
          </p>

          <template #footer>
            <div class="flex justify-end">
              <UButton
                variant="ghost"
                :color="lesson.buttonColor as any"
                trailing-icon="i-lucide-arrow-right"
                :to="lesson.path"
                @click.stop
              >
                Start lesson
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
    title: 'The Mechanism of Panic',
    description: 'Learn how your body\'s alarm system works (fight or flight) and why it is harmless, even if it is unpleasant.',
    icon: 'i-lucide-brain-circuit',
    color: '#3b82f6', // blue-500
    colorBase: 'blue',
    buttonColor: 'blue',
    path: '/mecanismul-panicii'
  },
  {
    title: 'The 5-4-3-2-1 Exercise',
    description: 'Grounding technique to bring you back to the present using your five senses.',
    icon: 'i-lucide-eye',
    color: '#10b981', // emerald-500
    colorBase: 'emerald',
    buttonColor: 'emerald',
    path: '/exercitiul-54321'
  },
  {
    title: '4-Count Breathing',
    description: 'Learn the Box Breathing technique used to quickly regulate the autonomic nervous system.',
    icon: 'i-lucide-wind',
    color: '#06b6d4', // cyan-500
    colorBase: 'cyan',
    buttonColor: 'cyan',
    path: '/respiratia-in-4-timpi'
  },
  {
    title: 'Dialogues of Change',
    description: 'Learn to challenge your catastrophic thoughts through the Socratic method and factual evidence.',
    icon: 'i-lucide-message-square-quote', // An icon that suggests dialogue and analysis
    color: '#3b82f6', // blue-500: Blue inspires confidence, calm, and logic (ideal for CBT)
    colorBase: 'blue',
    buttonColor: 'blue',
    path: '/dialoguri-de-schimbare'
  }
]
</script>