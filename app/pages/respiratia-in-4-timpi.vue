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
          4-Count Breathing
        </h1>
        <p class="text-slate-500 dark:text-slate-400">
          Follow the circle and breathe in its rhythm.
        </p>
      </div>

      <!-- Breathing animation -->
      <div class="relative flex items-center justify-center h-72 w-72">
        <!-- Base circle (guide) -->
        <div class="absolute w-64 h-64 rounded-full border-2 border-cyan-100 dark:border-cyan-900/30" />

        <!-- Animated circle -->
        <div
          class="rounded-full bg-gradient-to-br transition-all duration-[4000ms] ease-linear flex items-center justify-center relative z-10"
          :class="[currentState.color, currentState.shadow]"
          :style="circleStyle"
        >
          <!-- Time and State -->
          <div class="absolute flex flex-col items-center text-white drop-shadow-md">
            <span class="text-5xl font-bold mb-1 tabular-nums">{{ timeLeft }}</span>
            <span class="text-xs uppercase tracking-widest opacity-90 font-medium">{{ currentState.label }}</span>
          </div>
        </div>
      </div>

      <!-- Progress indicators for the 4 steps -->
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
          I am calmer
        </UButton>
      </div>
    </footer>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted, onUnmounted } from 'vue'

const duration = 4 // 4 seconds for each step

const currentStepIndex = ref(0)
const timeLeft = ref(duration)
const isAnimating = ref(false)

const steps = [
  { label: 'Inhale', scale: 1.1, color: 'from-cyan-400 to-blue-500', shadow: 'shadow-[0_0_40px_rgba(6,182,212,0.4)]' },
  { label: 'Hold', scale: 1.1, color: 'from-blue-500 to-indigo-500', shadow: 'shadow-[0_0_40px_rgba(59,130,246,0.4)]' },
  { label: 'Exhale', scale: 0.7, color: 'from-indigo-400 to-cyan-500', shadow: 'shadow-[0_0_20px_rgba(99,102,241,0.2)]' },
  { label: 'Hold', scale: 0.7, color: 'from-slate-400 to-slate-500', shadow: 'shadow-none' }
]

const currentState = computed(() => steps[currentStepIndex.value]!)

const circleStyle = computed(() => {
  // Force the circle to start from the small scale (0.7), then use the actual state scale
  const currentScale = isAnimating.value ? currentState.value.scale : 0.7
  return {
    width: `${currentScale * 14}rem`,
    height: `${currentScale * 14}rem`,
    opacity: currentState.value.label === 'Hold' ? 0.85 : 1
  }
})

let timer: ReturnType<typeof setInterval> | null = null

const runTimer = () => {
  timer = setInterval(() => {
    if (timeLeft.value > 1) {
      timeLeft.value--
    } else {
      // Move to the next step
      currentStepIndex.value = (currentStepIndex.value + 1) % steps.length
      timeLeft.value = duration
    }
  }, 1000)
}

onMounted(() => {
  // Allow the DOM to initially render the small circle, then start the animation
  setTimeout(() => {
    isAnimating.value = true
  }, 50)
  runTimer()
})

onUnmounted(() => {
  if (timer) clearInterval(timer)
})
</script>