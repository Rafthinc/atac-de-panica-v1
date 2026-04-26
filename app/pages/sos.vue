<template>
  <div class="flex-1 flex flex-col items-center justify-center p-6 bg-slate-50 dark:bg-slate-950">
    <div class="max-w-md w-full space-y-12">
      <!-- Header minimalist -->
      <div class="text-center">
        <h2 class="text-2xl font-medium text-slate-800 dark:text-slate-100 mb-2">
          Respiră odată cu cercul
        </h2>
        <p class="text-slate-500">
          Ești în siguranță. Totul va fi bine.
        </p>
      </div>

      <!-- Pasul 1: Ancorarea fizică (Cercul de respirație) -->
      <div class="flex justify-center py-10 relative h-64">
        <!-- Text ajutător central -->
        <div class="absolute inset-0 flex items-center justify-center z-10 pointer-events-none">
          <span
            class="text-sky-800 dark:text-sky-100 font-medium text-lg transition-opacity duration-1000"
            :class="{ 'opacity-100': isBreathingIn, 'opacity-50': !isBreathingIn }"
          >
            {{ isBreathingIn ? 'Inspiră...' : 'Expiră...' }}
          </span>
        </div>

        <!-- Cercul animat -->
        <div
          class="w-48 h-48 rounded-full bg-sky-200 dark:bg-sky-800 opacity-50 mix-blend-multiply dark:mix-blend-screen filter blur-xl transition-all duration-[4000ms] ease-in-out"
          :class="isBreathingIn ? 'scale-150' : 'scale-75'"
        />
        <div
          class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-48 h-48 rounded-full bg-sky-300 dark:bg-sky-700 opacity-60 transition-all duration-[4000ms] ease-in-out"
          :class="isBreathingIn ? 'scale-110' : 'scale-90'"
        />
      </div>

      <!-- Pasul 2: Restructurare cognitivă -->
      <div class="space-y-4">
        <h3 class="text-center text-lg font-medium text-slate-700 dark:text-slate-300">
          Ce simți acum? (Alege o opțiune)
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
              class="w-full justify-start text-left shadow-sm py-3 text-slate-600 dark:text-slate-600"
              @click="selectSymptom(symptom)"
            >
              {{ symptom.label }}
            </UButton>

            <!-- Mesajul de validare mutat aici -->
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

      <!-- Acțiune finală -->
      <div class="pt-8 flex justify-center">
        <UButton
          to="/progres"
          color="success"
          size="xl"
          icon="i-lucide-check-circle"
          class="w-full justify-center py-4 text-lg font-medium rounded-xl shadow-md hover:shadow-lg transition-all"
        >
          Sunt mai bine
        </UButton>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'

// Stocăm timpul la care s-a intrat pe pagină și o stare globală pentru progres
const sessionDuration = useState<string>('sessionDuration', () => '~4 min')
let startTime = 0

// Respirație
const isBreathingIn = ref(true)
let breathInterval: ReturnType<typeof setInterval>

onMounted(() => {
  startTime = Date.now()

  // Ciclu de 4 secunde inspir, 4 secunde expir
  breathInterval = setInterval(() => {
    isBreathingIn.value = !isBreathingIn.value
  }, 4000)
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

// Simptome și mesaje de reasigurare
const symptoms = [
  {
    id: 'heart',
    label: 'Inima îmi bate foarte tare',
    message: 'Este un răspuns normal la adrenalină. Inima ta pompează mai eficient pentru a oxigenarea mușchii. Este un semn de forță și sănătate, nu de pericol.'
  },
  {
    id: 'air',
    label: 'Simt că nu am aer',
    message: 'Simți asta pentru că pieptul e încordat și oxigenul e deja la nivel maxim în sânge. Concentrează-te doar pe a lăsa aerul să iasă încet, ca printr-un pai.'
  },
  {
    id: 'dizzy',
    label: 'Sunt amețit/ă',
    message: 'Amețeala este cauzată de o ușoară schimbare a nivelului de dioxid de carbon din cauza respirației rapide. Este inofensivă și trece imediat ce ritmul respirator se stabilizează.'
  },
  {
    id: 'faint',
    label: 'Simt că voi leșina',
    message: 'Leșinul apare la scăderea tensiunii. În panică, tensiunea crește ușor, făcând leșinul imposibil din punct de vedere biologic. Rămâi pe picioarele tale, ești în siguranță.'
  },
  {
    id: 'stroke',
    label: 'Voi face un accident vascular',
    message: 'Presiunea pe care o simți în cap este cauzată de tensiunea musculară de la nivelul gâtului și scalpului. Creierul tău este perfect protejat și oxigenat.'
  },
  {
    id: 'fall',
    label: 'Voi cădea / Picioare de vată',
    message: 'Picioarele se simt moi din cauza vasodilatației: vasele de sânge se dilată pentru a hrăni mușchii cu energie. Deși se simt ciudat, mușchii tăi sunt de fapt mai puternici acum.'
  },
  {
    id: 'mad',
    label: 'Simt că înnebunesc',
    message: 'Aceasta este doar o stare de „supra-veghe”. Creierul tău scanează mediul atât de intens încât totul pare ireal. Este o funcție de protecție, nu o pierdere a rațiunii.'
  },
  {
    id: 'choke',
    label: 'Nod în gât / Sufocare',
    message: 'Mușchii gâtului sunt pur și simplu contractați de stres. Căile respiratorii sunt complet libere. Încearcă să înghiți puțină apă sau doar să expiri lung.'
  }
]

const activeSymptomId = ref<string | null>(null)

const selectSymptom = (symptom: typeof symptoms[0]) => {
  // Închide mesajul dacă se apasă pe același buton, altfel îl deschide pe cel nou
  activeSymptomId.value = activeSymptomId.value === symptom.id ? null : symptom.id
}
</script>
