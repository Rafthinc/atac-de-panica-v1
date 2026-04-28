<template>
  <div class="flex-1 flex flex-col items-center min-h-[calc(100vh-theme(spacing.16))] bg-slate-50 dark:bg-slate-950 p-6">
    <header class="w-full max-w-md mb-8 pt-4">
      <div class="flex justify-between text-xs uppercase tracking-widest text-slate-500 dark:text-slate-400 mb-3 font-medium">
        <span>Grounding Exercise</span>
        <span>Step {{ currentStep + 1 }} / 5</span>
      </div>
      <div class="h-2 w-full bg-slate-200 dark:bg-slate-800 rounded-full overflow-hidden">
        <div 
          class="h-full bg-emerald-500 dark:bg-emerald-400 shadow-[0_0_10px_rgba(16,185,129,0.4)] transition-all duration-1000 ease-in-out"
          :style="{ width: `${((currentStep + 1) / 5) * 100}%` }"
        ></div>
      </div>
    </header>

    <main class="w-full max-w-md flex-1 flex flex-col justify-center relative overflow-hidden">
      <Transition name="fade-scale" mode="out-in">
        <div :key="currentStep" class="text-center space-y-10 w-full">
          <div class="flex justify-center">
            <div class="w-28 h-28 rounded-full bg-emerald-100 dark:bg-emerald-500/10 border border-emerald-200 dark:border-emerald-500/30 flex items-center justify-center animate-pulse shadow-inner">
              <UIcon :name="currentStepData.icon" class="w-14 h-14 text-emerald-600 dark:text-emerald-400" />
            </div>
          </div>

          <div class="space-y-6">
            <h2 class="text-6xl font-bold text-emerald-500 dark:text-emerald-400 drop-shadow-sm">
              {{ currentStepData.number }}
            </h2>
            <h1 class="text-2xl font-light leading-snug text-slate-800 dark:text-slate-100">
              {{ currentStepData.instruction }}
            </h1>
          </div>

          <!-- Progress dots for the current step -->
          <div class="flex justify-center gap-4 mt-8 h-8 items-center">
            <div 
              v-for="i in currentStepData.count" 
              :key="i"
              :class="[
                'w-5 h-5 rounded-full border-2 transition-all duration-300',
                itemsFound >= i 
                  ? 'bg-emerald-500 border-emerald-500 scale-125 shadow-md shadow-emerald-500/30' 
                  : 'border-slate-300 dark:border-slate-700 bg-transparent'
              ]"
            ></div>
          </div>
        </div>
      </Transition>
    </main>

    <footer class="w-full max-w-md mt-12 pb-8">
      <UButton 
        @click="itemClick"
        size="xl"
        color="success"
        class="w-full justify-center py-5 text-xl font-bold rounded-2xl shadow-lg transition-transform active:scale-[0.98]"
        :variant="isStepComplete ? 'solid' : 'outline'"
      >
        <template v-if="!isStepComplete">
          I found one
        </template>
        <template v-else>
          Move to next sense <UIcon name="i-lucide-arrow-right" class="ml-2 w-5 h-5" />
        </template>
      </UButton>
      
      <div class="mt-4">
        <UButton 
          to="/invata"
          variant="ghost"
          color="neutral"
          size="lg"
          class="w-full justify-center py-3 text-slate-500 dark:text-slate-400"
        >
          Stop exercise
        </UButton>
      </div>
    </footer>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import { useRouter } from '#vue-router'

const router = useRouter()
const currentStep = ref(0)
const itemsFound = ref(0)

const steps = [
  { number: "5", instruction: "Things you can SEE around you", icon: "i-lucide-eye", count: 5 },
  { number: "4", instruction: "Things you can TOUCH right now", icon: "i-lucide-hand", count: 4 },
  { number: "3", instruction: "Sounds you can HEAR", icon: "i-lucide-ear", count: 3 },
  { number: "2", instruction: "Smells you can NOTICE", icon: "i-lucide-flower-2", count: 2 },
  { number: "1", instruction: "Thing you can TASTE", icon: "i-lucide-utensils", count: 1 }
]

const currentStepData = computed(() => steps[currentStep.value]!)
const isStepComplete = computed(() => itemsFound.value >= currentStepData.value.count)

const itemClick = () => {
  // If the step is complete, move to the next one
  if (isStepComplete.value) {
    if (currentStep.value < steps.length - 1) {
      currentStep.value++
      itemsFound.value = 0
    } else {
      // End of exercise, go to the progress page
      router.push('/progres')
    }
  } else {
    // Otherwise, check off an item
    itemsFound.value++
    // Haptic feedback on mobile devices (if supported)
    try {
      if (typeof navigator !== 'undefined' && navigator.vibrate) {
        navigator.vibrate(50)
      }
    } catch (e) {
      // ignore error
    }
  }
}
</script>

<style scoped>
.fade-scale-enter-active, .fade-scale-leave-active {
  transition: all 0.5s cubic-bezier(0.34, 1.56, 0.64, 1);
}
.fade-scale-enter-from { 
  opacity: 0; 
  transform: scale(0.9) translateY(20px); 
}
.fade-scale-leave-to { 
  opacity: 0; 
  transform: scale(1.1) translateY(-20px); 
}
</style>