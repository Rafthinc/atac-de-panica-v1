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
          Cognitive Restructuring
        </h1>
      </div>
      <div
        v-if="selectedFear"
        class="space-y-2"
      >
        <div class="flex justify-end text-[10px] uppercase tracking-[0.2em] text-blue-400">
          <span>Step {{ currentQuestionIndex + 1 }} / 3</span>
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
            What thought is weighing on you?
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
                        Think again
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
    feedback.value = { type: 'success', message: 'Correct! You applied logic perfectly.', selectedIdx: idx }

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
    thought: 'I will have a heart attack',
    questions: [
      {
        q: 'What proof do you have that this time you will have a heart attack, considering you never had one in the hundreds of times you thought so?',
        options: [
          { text: 'No factual proof, just what I feel', correct: true },
          { text: 'The fact that my heart beats fast', correct: false, explanation: 'Increased heart rate is just a sign that you have adrenaline in your blood, not proof of a heart attack. It is a natural "fight or flight" response.' }
        ]
      },
      {
        q: 'What is the mathematical probability of having a heart attack now, if your heart resisted perfectly in all 50 previous crises?',
        options: [
          { text: 'Almost zero', correct: true },
          { text: 'Very high, my body is giving up', correct: false, explanation: 'The heart is a muscle that trains with effort, not a machine that breaks from panic.' }
        ]
      },
      {
        q: 'If a friend had the same symptoms and survived 100 times, would you tell him he will die now or that it\'s just panic?',
        options: [
          { text: 'I would tell him it\'s just panic', correct: true },
          { text: 'I would take him straight to the ER', correct: false, explanation: 'We are often much more objective with others than with ourselves. Apply the same rational compassion to yourself.' }
        ]
      }
    ]
  },
  {
    id: 'faint',
    thought: 'I will faint',
    questions: [
      {
        q: 'Did you know that fainting occurs when blood pressure drops, and in panic blood pressure INCREASES? Can the heart do both at once?',
        options: [
          { text: 'It\'s biologically impossible to faint', correct: true },
          { text: 'It doesn\'t matter, I feel like falling', correct: false, explanation: 'The sensation comes from hyperventilation (too much oxygen) and muscle tension, it\'s not real fainting.' }
        ]
      },
      {
        q: 'How many times have you felt like fainting and still stood on your feet until the end of the crisis?',
        options: [
          { text: 'Every single time', correct: true },
          { text: 'Surely this will be the first time', correct: false, explanation: 'This is "fortune telling". Rely on the dozens of previous experiences where you stayed perfectly conscious.' }
        ]
      },
      {
        q: 'What is more likely: the laws of physiology changing now, or just harmless dizziness from breathing?',
        options: [
          { text: 'A harmless dizziness', correct: true },
          { text: 'My body is giving out', correct: false, explanation: 'Emotional reasoning lies to you: the feeling of danger does not mean the danger is real.' }
        ]
      }
    ]
  },
  {
    id: 'stroke',
    thought: 'I will have a stroke',
    questions: [
      {
        q: 'Do you have real speech difficulties or paralysis, or is it just a tingling sensation caused by rapid oxygenation?',
        options: [
          { text: 'It\'s just a numbness sensation', correct: true },
          { text: 'What if it turns into paralysis?', correct: false, explanation: 'Tingling comes from the blood\'s pH change caused by breathing too fast. They are perfectly harmless.' }
        ]
      },
      {
        q: 'If a doctor told you your blood vessels are healthy, who is right: the doctor or the panic?',
        options: [
          { text: 'The doctor and the facts', correct: true },
          { text: 'The panic, I feel it too strongly', correct: false, explanation: 'Intense physical symptoms are produced by fear, not the state of your internal organs.' }
        ]
      },
      {
        q: 'The tingling has been appearing for 10 minutes. If it were a stroke, would the condition improve by itself during breathing?',
        options: [
          { text: 'No, the fact that it passes confirms the panic', correct: true },
          { text: 'It\'s dangerous anyway', correct: false, explanation: 'Strokes don\'t "come and go" on command of a breathing exercise. Panic does.' }
        ]
      }
    ]
  },
  {
    id: 'control',
    thought: 'I will go crazy / I\'m losing control',
    questions: [
      {
        q: 'Going crazy means losing touch with reality. Does the fact that you are so aware of how you feel indicate madness or hyper-vigilance?',
        options: [
          { text: 'Hyper-vigilance (too much attention)', correct: true },
          { text: 'I feel like I\'m losing my mind', correct: false, explanation: 'Those who have a psychotic episode lose awareness of their state. Your deep fear is exactly the proof of your intact reason.' }
        ]
      },
      {
        q: 'If you had lost control, would you still be able to follow this quiz right now?',
        options: [
          { text: 'The fact that I answer proves control', correct: true },
          { text: 'It\'s just a matter of time', correct: false, explanation: 'Loss of control would mean the inability to logically process information. You are still reading and analyzing perfectly.' }
        ]
      },
      {
        q: 'Has anyone ever completely lost control during a panic attack?',
        options: [
          { text: 'No one has ever gone crazy from panic', correct: true },
          { text: 'My mind can\'t handle it anymore', correct: false, explanation: 'Your brain is stressed by too much adrenaline, it is not "broken". Panic does not trigger madness.' }
        ]
      }
    ]
  },
  {
    id: 'choke',
    thought: 'I will suffocate',
    questions: [
      {
        q: 'If your throat were blocked, would you still be able to swallow saliva or speak?',
        options: [
          { text: 'I can swallow, so air goes in', correct: true },
          { text: 'I feel it closing', correct: false, explanation: 'The lump in the throat sensation is given by the contraction of the throat muscles from stress, it is not a physical blockage of the trachea.' }
        ]
      },
      {
        q: 'The lump in the throat sensation is a muscle contraction. Can a contracted muscle stop automatic breathing?',
        options: [
          { text: 'No, breathing is autonomic', correct: true },
          { text: 'I will forget to breathe', correct: false, explanation: 'The breathing reflex is controlled by the brainstem and cannot be stopped by tense muscles. Your body will breathe on its own.' }
        ]
      },
      {
        q: 'You\'ve been through this dozens of times. Have you ever truly suffocated?',
        options: [
          { text: 'No, I have always survived', correct: true },
          { text: 'Now I will suffocate for sure', correct: false, explanation: 'Your fear is trying to find a new excuse. Stick to the facts: you have survived in 100% of previous cases.' }
        ]
      }
    ]
  },
  {
    id: 'fall',
    thought: 'I will collapse',
    questions: [
      {
        q: 'Your legs feel weak because the blood vessels have dilated for effort. Is this weakness or preparation for action?',
        options: [
          { text: 'Preparation for action (flight)', correct: true },
          { text: 'My body is too weak', correct: false, explanation: 'On the contrary! Your muscles have received more blood to keep you safe from potential danger. They are stronger now.' }
        ]
      },
      {
        q: 'Have you ever physically collapsed during an attack, or did you just hold on to something out of fear?',
        options: [
          { text: 'I have never fallen because of panic', correct: true },
          { text: 'I fainted once', correct: false, explanation: 'Falling for other reasons (low blood pressure, lack of sugar) is completely different. Panic increases blood pressure, keeping you on your feet.' }
        ]
      },
      {
        q: 'If your legs had truly given out, would you still be able to stand or sit in a chair right now?',
        options: [
          { text: 'My legs are still supporting me well', correct: true },
          { text: 'I\'m just struggling not to fall', correct: false, explanation: 'Your balance is intact. The slight dizziness induced by rapid breathing only distorts your spatial perception.' }
        ]
      }
    ]
  },
  {
    id: 'death',
    thought: 'I will die',
    questions: [
      {
        q: 'If panic were fatal, would there still be millions of people suffering from it for decades?',
        options: [
          { text: 'It\'s just a false alarm reaction', correct: true },
          { text: 'My heart will fail soon', correct: false, explanation: 'Panic causes only moderate effort for the body, completely harmless in the short term, comparable to a light jog.' }
        ]
      },
      {
        q: 'What is the death rate caused strictly by a panic attack in the history of medicine?',
        options: [
          { text: 'Absolutely zero', correct: true },
          { text: 'Some surely die from this', correct: false, explanation: 'A panic attack is, biologically speaking, a mechanism perfectly designed to SAVE YOU from danger, not to kill you.' }
        ]
      },
      {
        q: 'Do you feel you are dying or do you feel extremely alive and alert (fast heart, active breathing)?',
        options: [
          { text: 'I am extremely alive and alert', correct: true },
          { text: 'I feel I\'m fading', correct: false, explanation: 'Adrenaline pumps blood to all vital organs. The sensation is overwhelming precisely because the body is working at full capacity.' }
        ]
      }
    ]
  },
  {
    id: 'trapped',
    thought: 'I can\'t get out of here / I\'m trapped',
    questions: [
      {
        q: 'Are you physically trapped or just blocked by the fear of embarrassing yourself if you leave?',
        options: [
          { text: 'I am blocked only by fear', correct: true },
          { text: 'I am physically trapped, I have no escape', correct: false, explanation: 'Look around. You can leave whenever you want, fear is what actually puts invisible barriers up.' }
        ]
      },
      {
        q: 'What would happen if you simply walked out? Would the world collapse or would your anxiety just reduce?',
        options: [
          { text: 'I would find out nothing happens', correct: true },
          { text: 'I would embarrass myself', correct: false, explanation: 'Even if some noticed, a moment of fleeting awkwardness is not a permanent catastrophe. You can leave to ensure your comfort.' }
        ]
      },
      {
        q: 'Is feeling trapped a sensation or a real fact right now?',
        options: [
          { text: 'It is just a temporary sensation', correct: true },
          { text: 'It is real, nobody can help me', correct: false, explanation: 'You don\'t need to be "saved". Adrenaline makes you want to escape. It is enough just to wait for its level to drop naturally.' }
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