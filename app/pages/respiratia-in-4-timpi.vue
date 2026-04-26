<template>
  <div class="flex-1 flex flex-col items-center min-h-[calc(100vh-theme(spacing.16))] bg-slate-50 dark:bg-slate-950 p-6">
    <header class="w-full max-w-md mb-12 pt-4 flex justify-between items-center">
      <UButton
        to="/invata"
        icon="i-lucide-arrow-left"
        variant="ghost"
        color="neutral"
        class="rounded-full"
      />
      <div class="text-xs uppercase tracking-widest text-slate-500 dark:text-slate-400 font-medium">
        <span>Box Breathing</span>
      </div>
      <div class="w-8" /> <!-- Spacer for centering -->
    </header>

    <main class="w-full max-w-md flex-1 flex flex-col items-center justify-center space-y-20 relative overflow-hidden">
      <!-- Info text -->
      <div class="text-center space-y-2">
        <h1 class="text-3xl font-bold text-slate-800 dark:text-slate-100">
          Respirație în 4 Timpi
        </h1>
        <p class="text-slate-500 dark:text-slate-400">
          Urmărește cercul și respiră în ritmul lui.
        </p>
      </div>

      <!-- Animația de respirație -->
      <div class="relative flex items-center justify-center h-72 w-72">
        <!-- Cercul de bază (ghidaj) -->
        <div class="absolute w-64 h-64 rounded-full border-2 border-cyan-100 dark:border-cyan-900/30" />

        <!-- Cercul animat -->
        <div
          class="rounded-full bg-gradient-to-br transition-all duration-[4000ms] ease-linear flex items-center justify-center relative z-10"
          :class="[currentState.color, currentState.shadow]"
          :style="circleStyle"
        >
          <!-- Timpul și Starea -->
          <div class="absolute flex flex-col items-center text-white drop-shadow-md">
            <span class="text-5xl font-bold mb-1 tabular-nums">{{ timeLeft }}</span>
            <span class="text-xs uppercase tracking-widest opacity-90 font-medium">{{ currentState.label }}</span>
          </div>
        </div>
      </div>

      <!-- Indicatori de progres pentru cei 4 pași -->
      <div class="flex gap-4">
        <div
          v-for="(step, index) in steps"
          :key="index"
          :class="[
            'h-1.5 rounded-full transition-all duration-500',
            currentStepIndex === index
              ? 'bg-cyan-500 dark:bg-cyan-400 w-16'
              : 'bg-slate-200 dark:bg-slate-800 w-8'
          ]"
        />
      </div>
    </main>

    <footer class="w-full max-w-md mt-12 pb-8">
      <div class="mt-4">
        <UButton
          to="/progres"
          color="primary"
          size="xl"
          class="w-full justify-center py-4 text-lg font-bold rounded-2xl shadow-lg transition-transform active:scale-[0.98]"
        >
          Sunt mai liniștit(ă)
        </UButton>
      </div>
    </footer>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted, onUnmounted } from 'vue'

const duration = 4 // 4 secunde pentru fiecare pas

const currentStepIndex = ref(0)
const timeLeft = ref(duration)
const isAnimating = ref(false)

const steps = [
  { label: 'Inspiră', scale: 1.1, color: 'from-cyan-400 to-blue-500', shadow: 'shadow-[0_0_40px_rgba(6,182,212,0.4)]' },
  { label: 'Menține', scale: 1.1, color: 'from-blue-500 to-indigo-500', shadow: 'shadow-[0_0_40px_rgba(59,130,246,0.4)]' },
  { label: 'Expiră', scale: 0.7, color: 'from-indigo-400 to-cyan-500', shadow: 'shadow-[0_0_20px_rgba(99,102,241,0.2)]' },
  { label: 'Menține', scale: 0.7, color: 'from-slate-400 to-slate-500', shadow: 'shadow-none' }
]

const currentState = computed(() => steps[currentStepIndex.value]!)

const circleStyle = computed(() => {
  // Forțăm cercul să plece de la scara mică (0.7), apoi folosim scala reală a stării
  const currentScale = isAnimating.value ? currentState.value.scale : 0.7
  return {
    width: `${currentScale * 14}rem`,
    height: `${currentScale * 14}rem`,
    opacity: currentState.value.label === 'Menține' ? 0.85 : 1
  }
})

let timer: ReturnType<typeof setInterval> | null = null

const runTimer = () => {
  timer = setInterval(() => {
    if (timeLeft.value > 1) {
      timeLeft.value--
    } else {
      // Trecem la pasul următor
      currentStepIndex.value = (currentStepIndex.value + 1) % steps.length
      timeLeft.value = duration
    }
  }, 1000)
}

onMounted(() => {
  // Permitem DOM-ului să randeze inițial cercul mic, apoi dăm startul animației
  setTimeout(() => {
    isAnimating.value = true
  }, 50)
  runTimer()
})

onUnmounted(() => {
  if (timer) clearInterval(timer)
})
</script>
