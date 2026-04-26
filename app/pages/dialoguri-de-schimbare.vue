<template>
  <div class="min-h-screen bg-slate-950 text-white p-6 flex flex-col items-center">
    <header class="w-full max-w-md mb-8 pt-4">
      <div class="flex items-center gap-3 mb-6">
        <UButton
          to="/"
          icon="i-lucide-arrow-left"
          variant="ghost"
          color="gray"
          class="rounded-full bg-slate-900 hover:bg-slate-800 text-slate-300"
        />
        <h1 class="text-xl font-medium text-slate-100">
          Restructurare Cognitivă
        </h1>
      </div>
      <div
        v-if="selectedFear"
        class="space-y-2"
      >
        <div class="flex justify-end text-[10px] uppercase tracking-[0.2em] text-blue-400">
          <span>Pas {{ currentQuestionIndex + 1 }} / 3</span>
        </div>
        <div class="h-1.5 w-full bg-slate-800 rounded-full overflow-hidden">
          <div
            class="h-full bg-blue-500 shadow-[0_0_10px_rgba(59,130,246,0.6)] transition-all duration-500"
            :style="{ width: `${((currentQuestionIndex + 1) / 3) * 100}%` }"
          />
        </div>
      </div>
    </header>

    <main class="w-full max-w-md flex-1">
      <Transition
        name="fade-slide"
        mode="out-in"
      >
        <div
          v-if="!selectedFear"
          class="space-y-6"
        >
          <h1 class="text-3xl font-bold text-blue-50 italic">
            Ce gând te apasă?
          </h1>
          <div class="grid gap-3">
            <button
              v-for="fear in dialogData"
              :key="fear.id"
              class="p-5 text-left rounded-2xl bg-slate-900 border border-slate-800 hover:border-blue-500/50 transition-all active:scale-95"
              @click="startQuiz(fear)"
            >
              <span class="text-lg font-semibold text-slate-200">{{ fear.thought }}</span>
            </button>
          </div>
        </div>

        <div
          v-else
          :key="currentQuestionIndex"
          class="space-y-8"
        >
          <div class="bg-blue-600/10 border border-blue-500/20 p-8 rounded-[2.5rem] shadow-2xl">
            <h2 class="text-2xl font-light leading-relaxed text-blue-50 antialiased">
              {{ selectedFear.questions[currentQuestionIndex].q }}
            </h2>
          </div>

          <div class="grid gap-4 relative">
            <div
              v-for="(option, idx) in selectedFear.questions[currentQuestionIndex].options"
              :key="idx"
              class="flex flex-col gap-2"
            >
              <button
                class="p-6 text-left rounded-2xl bg-slate-900 border-2 transition-all shadow-lg flex items-center justify-between gap-4"
                :class="[
                  (!feedback || feedback.selectedIdx !== idx) ? 'border-slate-800 hover:border-blue-500 text-white active:scale-95' : '',
                  (feedback && feedback.selectedIdx === idx && !option.correct) ? 'border-red-500 bg-red-500/10 text-white' : '',
                  (feedback && feedback.selectedIdx === idx && option.correct) ? 'border-green-500 bg-green-500/10 text-white' : ''
                ]"
                @click="handleAnswer(option, idx)"
              >
                <span class="font-bold text-lg">{{ option.text }}</span>
                <UIcon
                  v-if="feedback && feedback.selectedIdx === idx && option.correct"
                  name="i-lucide-check"
                  class="w-6 h-6 text-green-500 flex-shrink-0"
                />
                <UIcon
                  v-if="feedback && feedback.selectedIdx === idx && !option.correct"
                  name="i-lucide-x"
                  class="w-6 h-6 text-red-500 flex-shrink-0"
                />
              </button>

              <Transition name="fade-slide">
                <div
                  v-if="feedback && feedback.selectedIdx === idx && feedback.type === 'error'"
                  :id="'feedback-' + idx"
                  class="p-5 rounded-2xl bg-red-950/40 border border-red-900 text-red-200"
                >
                  <div class="flex items-start gap-3">
                    <UIcon
                      name="i-lucide-alert-triangle"
                      class="w-6 h-6 flex-shrink-0 text-red-400 mt-0.5"
                    />
                    <div>
                      <p class="font-bold text-red-300 mb-1">
                        Mai gândește-te o dată
                      </p>
                      <p class="text-sm leading-relaxed">
                        {{ feedback.message }}
                      </p>
                    </div>
                  </div>
                </div>
                <div
                  v-else-if="feedback && feedback.selectedIdx === idx && feedback.type === 'success'"
                  :id="'feedback-' + idx"
                  class="p-5 rounded-2xl bg-green-950/40 border border-green-900 text-green-200"
                >
                  <div class="flex items-center gap-3">
                    <UIcon
                      name="i-lucide-check-circle"
                      class="w-6 h-6 flex-shrink-0 text-green-400"
                    />
                    <p class="font-bold text-green-300">
                      {{ feedback.message }}
                    </p>
                  </div>
                </div>
              </Transition>
            </div>
          </div>
        </div>
      </Transition>
    </main>
  </div>
</template>

<script setup>
import { ref, nextTick } from 'vue'

const selectedFear = ref(null)
const currentQuestionIndex = ref(0)
const feedback = ref(null)

const startQuiz = (fear) => {
  selectedFear.value = fear
  currentQuestionIndex.value = 0
  feedback.value = null
}

const handleAnswer = async (option, idx) => {
  // Previne click-urile adiționale în timp ce se afișează feedback-ul de succes
  if (feedback.value?.type === 'success') return

  if (!option.correct) {
    feedback.value = { type: 'error', message: option.explanation, selectedIdx: idx }
  } else {
    feedback.value = { type: 'success', message: 'Corect! Ai aplicat perfect rațiunea.', selectedIdx: idx }

    setTimeout(() => {
      feedback.value = null
      if (currentQuestionIndex.value < 2) {
        currentQuestionIndex.value++
      } else {
        selectedFear.value = null
        navigateTo('/progres')
      }
    }, 1800)
  }

  await nextTick()
  setTimeout(() => {
    const el = document.getElementById(`feedback-${idx}`)
    if (el) {
      el.scrollIntoView({ behavior: 'smooth', block: 'center' })
    }
  }, 50)
}

const dialogData = [
  {
    id: 'heart',
    thought: 'Voi face un atac de cord',
    questions: [
      {
        q: 'Ce dovadă ai că de data aceasta vei face un atac de cord, având în vedere că nu ai făcut niciodată unul în sutele de dăți când ai crezut asta?',
        options: [
          { text: 'Nicio dovadă factuală, doar ce simt', correct: true },
          { text: 'Faptul că inima bate tare', correct: false, explanation: 'Ritmul cardiac crescut este doar un semn că ai adrenalină în sânge, nu o dovadă a unui infarct. Este un răspuns natural de "luptă sau fugi".' }
        ]
      },
      {
        q: 'Care este probabilitatea matematică să faci un infarct acum, dacă inima ta a rezistat perfect la toate cele 50 de crize anterioare?',
        options: [
          { text: 'Aproape zero', correct: true },
          { text: 'Foarte mare, corpul cedează', correct: false, explanation: 'Inima este un mușchi care se antrenează la efort, nu o mașinărie care se strică de la panică.' }
        ]
      },
      {
        q: 'Dacă un prieten ar avea aceleași simptome și ar fi supraviețuit de 100 de ori, i-ai spune că va muri acum sau că e doar panică?',
        options: [
          { text: 'I-aș spune că e doar panică', correct: true },
          { text: 'L-aș duce direct la urgențe', correct: false, explanation: 'Adesea suntem mult mai obiectivi cu alții decât cu noi înșine. Aplică aceeași compasiune rațională și pentru tine.' }
        ]
      }
    ]
  },
  {
    id: 'faint',
    thought: 'Voi leșina',
    questions: [
      {
        q: 'Știai că leșinul apare când tensiunea scade, iar în panică tensiunea CREȘTE? Poate inima să facă ambele lucruri deodată?',
        options: [
          { text: 'E biologic imposibil să leșin', correct: true },
          { text: 'Nu contează, simt că pic', correct: false, explanation: 'Senzația vine de la hiperventilație (prea mult oxigen) și tensiune musculară, nu este un leșin real.' }
        ]
      },
      {
        q: 'De câte ori ai simțit că leșini și totuși ai rămas în picioare până la finalul crizei?',
        options: [
          { text: 'De fiecare dată', correct: true },
          { text: 'Sigur acum va fi prima oară', correct: false, explanation: 'Acesta e "ghicitul viitorului". Bazează-te pe zecile de experiențe anterioare în care ai rămas perfect conștient.' }
        ]
      },
      {
        q: 'Ce este mai probabil: să se schimbe legile fiziologiei acum, sau să fie doar o amețeală inofensivă de la respirație?',
        options: [
          { text: 'O amețeală inofensivă', correct: true },
          { text: 'Corpul meu cedează', correct: false, explanation: 'Raționamentul emoțional te minte: senzația de pericol nu înseamnă că pericolul este real.' }
        ]
      }
    ]
  },
  {
    id: 'stroke',
    thought: 'Voi face un accident vascular',
    questions: [
      {
        q: 'Ai dificultăți reale de vorbire sau paralizie, ori este doar o senzație de furnicături cauzată de oxigenarea rapidă?',
        options: [
          { text: 'Este doar o senzație de amorțeală', correct: true },
          { text: 'Dacă se transformă în paralizie?', correct: false, explanation: 'Furnicăturile vin din schimbarea pH-ului sângelui cauzată de respirația prea rapidă. Sunt perfect inofensive.' }
        ]
      },
      {
        q: 'Dacă un medic ți-ar spune că vasele tale de sânge sunt sănătoase, cine are dreptate: medicul sau panica?',
        options: [
          { text: 'Medicul și faptele', correct: true },
          { text: 'Panica, o simt prea puternic', correct: false, explanation: 'Simptomele fizice intense sunt produse de frică, nu de starea organelor tale interne.' }
        ]
      },
      {
        q: 'Furnicăturile apar de 10 minute. Dacă era un AVC, starea se îmbunătățea de la sine în timpul respirației?',
        options: [
          { text: 'Nu, faptul că trece confirmă panica', correct: true },
          { text: 'Oricum este periculos', correct: false, explanation: 'AVC-urile nu "vin și trec" la comanda unui exercițiu de respirație. Panica da.' }
        ]
      }
    ]
  },
  {
    id: 'control',
    thought: 'Voi înnebuni / Pierd controlul',
    questions: [
      {
        q: 'A înnebuni înseamnă pierderea contactului cu realitatea. Faptul că ești atât de conștient de ce simți indică nebunie sau hiper-vigilență?',
        options: [
          { text: 'Hiper-vigilență (prea multă atenție)', correct: true },
          { text: 'Simt că îmi pierd mințile', correct: false, explanation: 'Cei care au un episod psihotic pierd conștientizarea stării lor. Frica ta profundă este tocmai dovada rațiunii tale intacte.' }
        ]
      },
      {
        q: 'Dacă ai fi pierdut controlul, ai mai fi capabil să urmezi acest quiz acum?',
        options: [
          { text: 'Faptul că răspund dovedește controlul', correct: true },
          { text: 'E doar o chestiune de timp', correct: false, explanation: 'Pierderea controlului ar însemna imposibilitatea de a procesa logic informații. Tu încă citești și analizezi perfect.' }
        ]
      },
      {
        q: 'A pierdut cineva vreodată controlul total în timpul unui atac de panică?',
        options: [
          { text: 'Nimeni nu a înnebunit din panică', correct: true },
          { text: 'Mintea mea nu mai face față', correct: false, explanation: 'Creierul tău este stresat de prea multă adrenalină, nu este "stricat". Panica nu declanșează nebunia.' }
        ]
      }
    ]
  },
  {
    id: 'choke',
    thought: 'Mă voi sufoca',
    questions: [
      {
        q: 'Dacă gâtul ar fi blocat, ai mai putea să înghiți salivă sau să vorbești?',
        options: [
          { text: 'Pot înghiți, deci aerul intră', correct: true },
          { text: 'Simt cum se închide', correct: false, explanation: 'Senzația de nod în gât este dată de contracția mușchilor gâtului de la stres, nu este un blocaj fizic al traheei.' }
        ]
      },
      {
        q: 'Senzația de nod în gât este o contracție musculară. Poate un mușchi contractat să oprească respirația automată?',
        options: [
          { text: 'Nu, respirația este autonomă', correct: true },
          { text: 'Voi uita să respir', correct: false, explanation: 'Reflexul de respirație este controlat de trunchiul cerebral și nu poate fi oprit de mușchii tensionați. Corpul tău va respira singur.' }
        ]
      },
      {
        q: 'Ai pățit asta de zeci de ori. Te-ai sufocat vreodată cu adevărat?',
        options: [
          { text: 'Nu, am supraviețuit mereu', correct: true },
          { text: 'Acum mă voi sufoca sigur', correct: false, explanation: 'Frica ta încearcă să găsească o scuză nouă. Rămâi la fapte: ai supraviețuit în 100% din cazurile anterioare.' }
        ]
      }
    ]
  },
  {
    id: 'fall',
    thought: 'Voi cădea din picioare',
    questions: [
      {
        q: 'Picioarele se simt moi pentru că vasele de sânge s-au dilatat pentru efort. Este asta slăbiciune sau pregătire pentru acțiune?',
        options: [
          { text: 'Pregătire pentru acțiune (fugă)', correct: true },
          { text: 'Corpul meu e prea slab', correct: false, explanation: 'Din contră! Mușchii tăi au primit mai mult sânge pentru a te feri de un potențial pericol. Sunt mai puternici acum.' }
        ]
      },
      {
        q: 'Te-ai prăbușit vreodată fizic în timpul unui atac, sau doar te-ai ținut de ceva de frică?',
        options: [
          { text: 'Nu am căzut niciodată din cauza panicii', correct: true },
          { text: 'Am leșinat odată', correct: false, explanation: 'A cădea din alte cauze (tensiune mică, lipsă de zahăr) este complet diferit. Panica crește tensiunea arterială, menținându-te pe picioare.' }
        ]
      },
      {
        q: 'Dacă picioarele ar fi cedat cu adevărat, ai mai putea sta acum în picioare sau pe scaun?',
        options: [
          { text: 'Picioarele încă mă susțin bine', correct: true },
          { text: 'Doar mă chinui să nu cad', correct: false, explanation: 'Echilibrul tău este intact. Amețeala ușoară indusă de respirația rapidă doar îți distorsionează percepția spațială.' }
        ]
      }
    ]
  },
  {
    id: 'death',
    thought: 'Voi muri',
    questions: [
      {
        q: 'Dacă panica ar fi fatală, ar mai exista milioane de oameni care suferă de ea de zeci de ani?',
        options: [
          { text: 'Este doar o reacție falsă de alarmă', correct: true },
          { text: 'Inima mea va ceda curând', correct: false, explanation: 'Panica provoacă doar un efort moderat pentru corp, complet inofensiv pe termen scurt, comparabil cu o alergare ușoară.' }
        ]
      },
      {
        q: 'Care este rata de deces cauzată strict de un atac de panică în istoria medicinei?',
        options: [
          { text: 'Absolut zero', correct: true },
          { text: 'Unii sigur mor din asta', correct: false, explanation: 'Atacul de panică este, biologic vorbind, un mecanism perfect conceput să TE SALVEZE de pericol, nu să te omoare.' }
        ]
      },
      {
        q: 'Simți că mori sau simți că ești extrem de viu și alertat (inimă rapidă, respirație activă)?',
        options: [
          { text: 'Sunt extrem de viu și alert', correct: true },
          { text: 'Simt că mă sting', correct: false, explanation: 'Adrenalina pompează sânge la toate organele vitale. Senzația este copleșitoare tocmai pentru că trupul lucrează la capacitate maximă.' }
        ]
      }
    ]
  },
  {
    id: 'trapped',
    thought: 'Nu pot ieși de aici / Sunt captiv',
    questions: [
      {
        q: 'Ești captiv fizic sau ești blocat doar de frica de a nu te face de râs dacă pleci?',
        options: [
          { text: 'Sunt blocat doar de frică', correct: true },
          { text: 'Sunt blocat fizic, nu am scăpare', correct: false, explanation: 'Privește în jur. Poți pleca oricând vrei, frica este cea care îți pune de fapt bariere invizibile.' }
        ]
      },
      {
        q: 'Ce s-ar întâmpla dacă ai ieși pur și simplu? S-ar prăbuși lumea sau doar s-ar reduce anxietatea?',
        options: [
          { text: 'Aș constata că nu se întâmplă nimic', correct: true },
          { text: 'M-aș face de râs', correct: false, explanation: 'Chiar dacă unii ar observa, o clipă de stânjeneală trecătoare nu este o catastrofă permanentă. Poți pleca pentru a-ți asigura confortul.' }
        ]
      },
      {
        q: 'Captivitatea este o senzație sau un fapt real în acest moment?',
        options: [
          { text: 'Este doar o senzație temporară', correct: true },
          { text: 'Este real, nimeni nu mă poate ajuta', correct: false, explanation: 'Nu ai nevoie să fii "salvat". Adrenalina te face să vrei să scapi. E suficient doar să aștepți ca nivelul ei să scadă natural.' }
        ]
      }
    ]
  }
]
</script>

<style scoped>
.fade-slide-enter-active, .fade-slide-leave-active {
  transition: all 0.4s ease;
}
.fade-slide-enter-from { opacity: 0; transform: translateY(20px); }
.fade-slide-leave-to { opacity: 0; transform: translateY(-20px); }

.antialiased {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
</style>
