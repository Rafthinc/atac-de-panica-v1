<template>
  <div class="flex-1 flex flex-col items-center min-h-[calc(100vh-theme(spacing.16))] bg-slate-50 dark:bg-slate-950 p-6">
    <header class="w-full max-w-md mb-8 pt-4">
      <div class="flex justify-between items-center mb-2 text-sm text-slate-500 dark:text-slate-400">
        <span class="font-medium text-slate-700 dark:text-slate-300">Lesson 1: The Mechanism</span>
        <span>{{ Math.round((currentStep / steps.length) * 100) }}%</span>
      </div>
      <div class="w-full bg-slate-200 dark:bg-slate-800 h-2 rounded-full overflow-hidden">
        <div 
          class="bg-sky-500 dark:bg-sky-400 h-full transition-all duration-500 ease-out" 
          :style="{ width: `${(currentStep / steps.length) * 100}%` }"
        ></div>
      </div>
    </header>

    <main class="w-full max-w-md flex-1 flex flex-col relative overflow-hidden">
      <div class="flex-1 flex flex-col justify-center">
        <Transition name="fade-slide" mode="out-in">
          <div :key="currentStep" class="w-full">
            <h1 class="text-3xl font-bold mb-8 leading-tight text-slate-800 dark:text-slate-100">
              {{ currentStepData.title }}
            </h1>
            
            <div class="bg-white dark:bg-slate-900 rounded-3xl p-8 shadow-xl border border-slate-100 dark:border-slate-800/50">
              <p class="text-lg leading-relaxed text-slate-600 dark:text-slate-300">
                {{ currentStepData.content }}
              </p>
              
              <div v-if="currentStepData.animation === 'alarm'" class="mt-12 mb-4 flex justify-center">
                <div class="relative">
                  <div class="absolute inset-0 bg-red-500/20 dark:bg-red-500/30 blur-2xl rounded-full animate-pulse"></div>
                  <div class="relative bg-red-50 dark:bg-red-500/10 border border-red-200 dark:border-red-500/30 p-8 rounded-full shadow-inner">
                    <UIcon name="i-lucide-bell-ring" class="w-16 h-16 text-red-500 dark:text-red-400" />
                  </div>
                </div>
              </div>

              <div v-if="currentStepData.animation === 'loop'" class="mt-12 mb-4 flex justify-center">
                <div class="relative">
                  <div class="absolute inset-0 bg-sky-500/20 blur-2xl rounded-full animate-spin-slow"></div>
                  <div class="relative bg-sky-50 dark:bg-sky-500/10 border border-sky-200 dark:border-sky-500/30 p-8 rounded-full shadow-inner">
                    <UIcon name="i-lucide-refresh-cw" class="w-16 h-16 text-sky-500 dark:text-sky-400" />
                  </div>
                </div>
              </div>

              <div v-if="currentStepData.animation === 'energy'" class="mt-12 mb-4 flex justify-center">
                <div class="relative">
                  <div class="absolute inset-0 bg-amber-500/20 blur-2xl rounded-full animate-bounce"></div>
                  <div class="relative bg-amber-50 dark:bg-amber-500/10 border border-amber-200 dark:border-amber-500/30 p-8 rounded-full shadow-inner">
                    <UIcon name="i-lucide-zap" class="w-16 h-16 text-amber-500 dark:text-amber-400" />
                  </div>
                </div>
              </div>
            </div>
          </div>
        </Transition>
      </div>

      <footer class="mt-12 space-y-4 pb-8">
        <UButton 
          @click="nextStep"
          size="xl"
          color="primary"
          class="w-full justify-center py-4 text-lg font-bold rounded-2xl shadow-lg transition-transform active:scale-[0.98]"
        >
          {{ currentStep === steps.length ? 'I understand' : 'Continue' }}
        </UButton>
        <UButton 
          v-if="currentStep > 1"
          @click="prevStep"
          variant="ghost"
          color="neutral"
          size="lg"
          class="w-full justify-center py-3 text-slate-500 dark:text-slate-400"
        >
          Back
        </UButton>
      </footer>
    </main>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'

const router = useRouter()
const currentStep = ref(1)

const steps = [
  {
    title: "Panic is an alarm system.",
    content: "Imagine there is a fire alarm in your body. Sometimes, it goes off even if there is no smoke. This is panic: a false alarm.",
    animation: "alarm"
  },
  {
    title: "Misinterpretation",
    content: "The brain feels the heart beating fast and concludes: 'There is a danger!'. The UX of panic is a feedback loop between a physical sensation and a catastrophic interpretation.",
    animation: "loop"
  },
  {
    title: "Adrenaline",
    content: "The sensations you feel (dizziness, trembling) are just the effects of adrenaline preparing the body for effort. They are not signs of illness, but of unused energy.",
    animation: "energy"
  }
]

const currentStepData = computed(() => steps[currentStep.value - 1]!)

const nextStep = () => {
  if (currentStep.value < steps.length) {
    currentStep.value++
  } else {
    router.push('/invata')
  }
}

const prevStep = () => {
  if (currentStep.value > 1) {
    currentStep.value--
  }
}
</script>

<style scoped>
.fade-slide-enter-active,
.fade-slide-leave-active {
  transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
}

.fade-slide-enter-from {
  opacity: 0;
  transform: translateX(20px);
}

.fade-slide-leave-to {
  opacity: 0;
  transform: translateX(-20px);
}

.animate-spin-slow {
  animation: spin 3s linear infinite;
}
</style>