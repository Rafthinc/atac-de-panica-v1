<template>
  <div class="flex-1 flex flex-col items-center justify-center p-6 bg-gradient-to-t from-emerald-50 to-white dark:from-emerald-950 dark:to-slate-950">
    <div class="max-w-md w-full space-y-10 text-center">
      <!-- Iconiță de succes / feedback vizual -->
      <div class="flex justify-center">
        <div class="w-32 h-32 bg-emerald-100 dark:bg-emerald-900/50 rounded-full flex items-center justify-center text-emerald-500 dark:text-emerald-400">
          <UIcon
            name="i-lucide-award"
            class="w-16 h-16"
          />
        </div>
      </div>

      <!-- Mesajul de confirmare -->
      <div class="space-y-4">
        <h1 class="text-3xl font-bold text-slate-800 dark:text-slate-100">
          Ai reușit!
        </h1>
        <p class="text-lg text-slate-600 dark:text-slate-400">
          Ai demonstrat încă o dată că poți face față. Ai trecut peste acest episod. Fii mândru/ă de tine!
        </p>
      </div>

      <!-- Statistici rapide (Micro-succes) -->
      <div class="bg-white dark:bg-slate-900 rounded-2xl shadow-sm border border-slate-100 dark:border-slate-800 p-6 flex justify-around">
        <div class="text-center">
          <p class="text-sm text-slate-500 mb-1">
            Durată
          </p>
          <p class="text-2xl font-semibold text-emerald-600 dark:text-emerald-400">
            {{ sessionDuration }}
          </p>
        </div>
        <div class="text-center">
          <p class="text-sm text-slate-500 mb-1">
            Status
          </p>
          <p class="text-2xl font-semibold text-emerald-600 dark:text-emerald-400">
            Reglat
          </p>
        </div>
      </div>

      <!-- Întoarcere la Home -->
      <div class="pt-6">
        <UButton
          to="/"
          color="neutral"
          variant="outline"
          size="lg"
          icon="i-lucide-home"
          class="px-8 rounded-full"
        >
          Înapoi acasă
        </UButton>
      </div>

      <!-- Date Contact / Profil Specialist -->
      <div class="bg-white/60 dark:bg-slate-900/60 backdrop-blur-md border border-emerald-100 dark:border-emerald-900/30 p-6 rounded-2xl shadow-sm text-left relative overflow-hidden mt-8">
        <div class="absolute top-0 left-0 w-1.5 h-full bg-emerald-400 dark:bg-emerald-500" />

        <div class="flex items-center gap-4 mb-4">
          <div class="w-12 h-12 bg-emerald-100 dark:bg-emerald-900/50 text-emerald-600 dark:text-emerald-400 rounded-full flex items-center justify-center flex-shrink-0">
            <UIcon
              name="i-lucide-user-check"
              class="w-6 h-6"
            />
          </div>
          <div>
            <h2 class="text-lg font-bold text-slate-800 dark:text-slate-100">
              Adrian-Francois
            </h2>
            <p class="text-[11px] font-medium text-emerald-600 dark:text-emerald-400 uppercase tracking-widest">
              Dezvoltator IT & Specialist
            </p>
          </div>
        </div>

        <p class="text-sm text-slate-700 dark:text-slate-300 mb-5 leading-relaxed">
          <strong>Psiholog, Psihoterapeut, Nutriționist & Dezvoltator IT.</strong><br>
          Specialist în optimizarea sănătății emoționale prin nutriție și psihologie.
        </p>

        <div class="space-y-3 pt-4 border-t border-slate-200/60 dark:border-slate-700/60">
          <a
            href="tel:0723179552"
            class="flex items-center gap-3 text-sm font-medium text-slate-600 dark:text-slate-300 hover:text-emerald-600 dark:hover:text-emerald-400 transition-colors"
          >
            <UIcon
              name="i-lucide-phone"
              class="w-4 h-4 text-emerald-500 flex-shrink-0"
            />
            0723 179 552
          </a>
          <a
            href="mailto:psihoterapiieficiente@gmail.com"
            class="flex items-center gap-3 text-sm font-medium text-slate-600 dark:text-slate-300 hover:text-emerald-600 dark:hover:text-emerald-400 transition-colors break-all"
          >
            <UIcon
              name="i-lucide-mail"
              class="w-4 h-4 text-emerald-500 flex-shrink-0"
            />
            psihoterapiieficiente@gmail.com
          </a>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
// Această pagină oferă feedback pur pozitiv.
// Într-o aplicație reală, timpul s-ar calcula pe baza momentului în care s-a intrat în SOS.
import JSConfetti from 'js-confetti'
import { onMounted } from 'vue'

// Preluăm starea globală calculată în pagina anterioară (dacă nu există, se afișează valoarea default)
const sessionDuration = useState<string>('sessionDuration', () => '🌟')

onMounted(() => {
  try {
    const jsConfetti = new JSConfetti()
    jsConfetti.addConfetti({
      emojis: ['✨', '🌿', '💚', '🌟'],
      confettiNumber: 40
    })
  } catch (e) {
    // Ignorăm eroarea dacă librăria nu e instalată, este un efect secundar opțional
  }
})
</script>
