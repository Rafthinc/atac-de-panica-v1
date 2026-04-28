<template>
  <div class="flex-1 flex flex-col items-center justify-center p-6 bg-slate-50 dark:bg-slate-950">
    <div class="max-w-md w-full space-y-12">
      <!-- Minimalist header -->
      <div class="text-center">
        <h2 class="text-2xl font-medium text-slate-800 dark:text-slate-100 mb-2">
          Breathe with the circle
        </h2>
        <p class="text-slate-500">
          You are safe. Everything will be fine.
        </p>
      </div>

      <!-- Step 1: Physical grounding (Breathing circle) -->
      <div class="flex justify-center py-10 relative h-64 w-full">
        <!-- Central helper text -->
        <div class="absolute inset-0 flex flex-col items-center justify-center z-10 pointer-events-none">
          <span class="text-5xl font-bold text-sky-800 dark:text-sky-100 tabular-nums drop-shadow-sm mb-1">
            {{ timeLeft }}
          </span>
          <span class="text-sky-800 dark:text-sky-100 font-medium text-sm uppercase tracking-widest drop-shadow-sm">
            {{ currentPhase.text }}
          </span>
        </div>

        <!-- Outer animated circle -->
        <div
          class="absolute top-1/2 left-1/2 w-48 h-48 rounded-full bg-sky-200 dark:bg-sky-800 opacity-50 mix-blend-multiply dark:mix-blend-screen filter blur-xl transition-transform ease-linear"
          :style="{
            transform: `translate(-50%, -50%) scale(${isAnimating ? currentPhase.outerScale : 0.6})`,
            transitionDuration: `${currentPhase.duration}s`
          }"
        />
        <!-- Inner animated circle -->
        <div
          class="absolute top-1/2 left-1/2 w-48 h-48 rounded-full bg-sky-300 dark:bg-sky-700 opacity-60 transition-transform ease-linear"
          :style="{
            transform: `translate(-50%, -50%) scale(${isAnimating ? currentPhase.innerScale : 0.5})`,
            transitionDuration: `${currentPhase.duration}s`
          }"
        />
      </div>

      <!-- Step 2: Cognitive restructuring -->
      <div class="space-y-4">
        <h3 class="text-center text-lg font-medium text-slate-700 dark:text-slate-300">
          What do you feel right now? (Choose an option)
        </h3>

        <div class="grid grid-cols-1 gap-3">
          <div
            v-for="symptom in symptoms"
            :key="symptom.id"
            class="flex flex-col"
          >
            <UButton
              color="neutral"
              variant="solid"
              size="lg"
              class="w-full justify-start text-left shadow-sm py-3 text-slate-800 dark:text-slate-100 bg-white hover:bg-slate-50 dark:bg-slate-900 dark:hover:bg-slate-800"
              @click="selectSymptom(symptom)"
            >
              {{ symptom.label }}
            </UButton>

            <!-- Validation message moved here -->
            <transition
              enter-active-class="transition-all duration-500 ease-out overflow-hidden"
              enter-from-class="opacity-0 max-h-0"
              enter-to-class="opacity-100 max-h-96"
              leave-active-class="transition-all duration-300 ease-in overflow-hidden"
              leave-from-class="opacity-100 max-h-96"
              leave-to-class="opacity-0 max-h-0"
            >
              <div v-if="activeSymptomId === symptom.id">
                <div class="mt-2 p-4 rounded-xl bg-green-50 dark:bg-green-900/30 border border-green-200 dark:border-green-800">
                  <p class="text-green-800 dark:text-green-200 font-medium text-center">
                    {{ symptom.message }}
                  </p>
                </div>
              </div>
            </transition>
          </div>
        </div>
      </div>

      <!-- Final action -->
      <div class="pt-8 flex justify-center">
        <UButton
          to="/progres"
          color="success"
          size="xl"
          icon="i-lucide-check-circle"
          class="w-full justify-center py-4 text-lg font-medium rounded-xl shadow-md hover:shadow-lg transition-all"
        >
          I am better
        </UButton>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted, onUnmounted } from 'vue'

// Store the time the page was entered and a global state for progress
const sessionDuration = useState<string>('sessionDuration', () => '~4 min')
let startTime = 0

// Breathing: Inhale (4s), Hold (2s), Exhale (6s)
const phases = [
  { text: 'Inhale', duration: 4, outerScale: 1.5, innerScale: 1.2 },
  { text: 'Hold', duration: 2, outerScale: 1.5, innerScale: 1.2 },
  { text: 'Exhale', duration: 6, outerScale: 0.6, innerScale: 0.5 }
]

const currentPhaseIndex = ref(0)
const currentPhase = computed(() => phases[currentPhaseIndex.value]!)
const timeLeft = ref(phases[0]!.duration)
const isAnimating = ref(false)

let breathInterval: ReturnType<typeof setInterval> | null = null

onMounted(() => {
  startTime = Date.now()

  // Allow the DOM to initially render the circles at minimum scale (small scale)
  setTimeout(() => {
    isAnimating.value = true
  }, 50)

  breathInterval = setInterval(() => {
    if (timeLeft.value > 1) {
      timeLeft.value--
    } else {
      currentPhaseIndex.value = (currentPhaseIndex.value + 1) % phases.length
      timeLeft.value = phases[currentPhaseIndex.value]!.duration
    }
  }, 1000)
})

onUnmounted(() => {
  if (startTime > 0) {
    const elapsedMs = Date.now() - startTime
    const minutes = Math.floor(elapsedMs / 60000)
    const seconds = Math.floor((elapsedMs % 60000) / 1000)

    sessionDuration.value = minutes > 0 ? `${minutes} min ${seconds} sec` : `${seconds} sec`
  }

  if (breathInterval) clearInterval(breathInterval)
})

// Symptoms and reassurance messages
const symptoms = [
  {
    id: 'heart',
    label: 'My heart is beating very fast',
    message: 'This is a normal response to adrenaline. Your heart pumps more efficiently to oxygenate the muscles. It is a sign of strength and health, not danger.'
  },
  {
    id: 'air',
    label: 'I feel like I have no air',
    message: 'You feel this because your chest is tight and the oxygen is already at maximum level in your blood. Just focus on letting the air out slowly, like through a straw.'
  },
  {
    id: 'dizzy',
    label: 'I am dizzy',
    message: 'Dizziness is caused by a slight change in carbon dioxide levels due to rapid breathing. It is harmless and passes as soon as the breathing rhythm stabilizes.'
  },
  {
    id: 'faint',
    label: 'I feel like I will faint',
    message: 'Fainting occurs when blood pressure drops. In panic, blood pressure increases slightly, making fainting biologically impossible. Stay on your feet, you are safe.'
  },
  {
    id: 'stroke',
    label: 'I will have a stroke',
    message: 'The pressure you feel in your head is caused by muscle tension in your neck and scalp. Your brain is perfectly protected and oxygenated.'
  },
  {
    id: 'fall',
    label: 'I will fall / Jelly legs',
    message: 'Your legs feel weak due to vasodilation: blood vessels dilate to feed the muscles with energy. Although they feel weird, your muscles are actually stronger now.'
  },
  {
    id: 'mad',
    label: 'I feel like I am going crazy',
    message: 'This is just a state of "hyper-arousal". Your brain is scanning the environment so intensely that everything seems unreal. It is a protective function, not a loss of sanity.'
  },
  {
    id: 'choke',
    label: 'Lump in throat / Choking',
    message: 'Your throat muscles are simply contracted by stress. Your airways are completely clear. Try to swallow a little water or just exhale long.'
  }
]

const activeSymptomId = ref<string | null>(null)

const selectSymptom = (symptom: typeof symptoms[0]) => {
  // Close the message if the same button is pressed, otherwise open the new one
  activeSymptomId.value = activeSymptomId.value === symptom.id ? null : symptom.id
}
</script>