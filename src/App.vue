```vue
<script setup>
import { computed, onBeforeUnmount, ref } from 'vue'
import CuteButton from './components/CuteButton.vue'
import FloatingHearts from './components/FloatingHearts.vue'
import ProgressIndicator from './components/ProgressIndicator.vue'
import SecretCard from './components/SecretCard.vue'
import { siteConfig as config } from './config'

const stage = ref('invitation')
const selectedAnswer = ref(null)
const openedCards = ref([])
const musicOn = ref(false)
const audio = ref(null)

const photoAvailable = ref(true)

const stageOrder = [
  'quiz',
  'secrets',
  'twist',
  'question',
]

const progress = computed(() => {
  if (stage.value === 'invitation') return 0

  if (
    stage.value === 'ending-yes' ||
    stage.value === 'ending-think'
  ) {
    return 5
  }

  return stageOrder.indexOf(stage.value) + 1
})

const showProgress = computed(() => {
  return ![
    'invitation',
    'ending-yes',
    'ending-think',
  ].includes(stage.value)
})

const allCardsOpened = computed(() => {
  return openedCards.value.length === config.secretCards.length
})

function go(nextStage) {
  stage.value = nextStage

  window.scrollTo({
    top: 0,
    behavior: 'smooth',
  })
}

async function startAdventure() {
  go('quiz')
  await toggleMusic(true)
}

async function toggleMusic(forceOn = null) {
  if (!audio.value) return

  const shouldPlay =
    forceOn === null ? !musicOn.value : forceOn

  if (shouldPlay) {
    try {
      await audio.value.play()
      musicOn.value = true
    } catch (error) {
      musicOn.value = false
      console.warn(
        'Musik belum dapat diputar. Periksa lokasi file audio.',
        error,
      )
    }
  } else {
    audio.value.pause()
    musicOn.value = false
  }
}

function chooseAnswer(index) {
  selectedAnswer.value = index
}

function continueFromQuiz() {
  if (selectedAnswer.value === null) return

  go('secrets')
}

function toggleCard(index) {
  if (openedCards.value.includes(index)) {
    openedCards.value = openedCards.value.filter(
      (item) => item !== index,
    )
  } else {
    openedCards.value = [...openedCards.value, index]
  }
}

function answerYes() {
  go('ending-yes')
}

function answerThink() {
  go('ending-think')
}

function replay() {
  selectedAnswer.value = null
  openedCards.value = []
  photoAvailable.value = true

  go('invitation')
}

function handlePhotoError() {
  photoAvailable.value = false
}

onBeforeUnmount(() => {
  if (audio.value) {
    audio.value.pause()
  }
})
</script>

<template>
  <main class="app-shell">
    <!-- DEKORASI BACKGROUND -->
    <FloatingHearts />

    <!-- HEADER -->
    <header class="topbar">
      <a
        href="#"
        class="brand"
        aria-label="Mulai ulang petualangan"
        @click.prevent="replay"
      >
        <span class="brand-bow">୨୧</span>
        <span>little crush club</span>
      </a>

      <button
        type="button"
        class="music-toggle"
        :aria-label="musicOn ? 'Jeda musik' : 'Putar musik'"
        :aria-pressed="musicOn"
        @click="toggleMusic()"
      >
        <span>{{ musicOn ? '♫' : '♪' }}</span>

        <span class="music-label">
          {{ musicOn ? 'music on' : 'music off' }}
        </span>
      </button>
    </header>

    <!-- INDIKATOR PROGRES -->
    <ProgressIndicator
      v-if="showProgress"
      :current="progress"
      :total="5"
    />

    <!-- TRANSISI ANTARHALAMAN -->
    <Transition name="page-pop" mode="out-in">
      <!-- ======================================
           1. HALAMAN PEMBUKA
           DESAIN AMPLOP TETAP DIPERTAHANKAN
      ======================================= -->
      <section
        v-if="stage === 'invitation'"
        key="invitation"
        class="screen screen--welcome"
      >
        <div class="hero-sticker sticker-star">
          ✦ just for you ✦
        </div>

        <!-- ILUSTRASI AMPLOP ORIGINAL -->
        <div
          class="envelope-scene"
          aria-label="Ilustrasi amplop pink berisi surat"
        >
          <div class="envelope-shadow"></div>

          <div class="envelope">
            <div class="envelope-back"></div>

            <div class="letter-peek">
              <span>hi, {{ config.recipientName }}!</span>
              <span class="letter-heart">♡</span>
            </div>

            <div class="envelope-flap"></div>
            <div class="envelope-front"></div>

            <div class="envelope-seal">♥</div>
            <div class="envelope-bow">୨୧</div>
          </div>
        </div>

        <p class="eyebrow">
          a very tiny, very secret mission
        </p>

        <h1>{{ config.headline }}</h1>

        <p class="intro-copy">
          hii,
          <strong>{{ config.recipientName }}</strong>!
          Ada sesuatu buat kamu.
          Tenang, bukan tagihan kok. Promise. 🎀
        </p>

        <CuteButton
          label="buka kejutan ini ♡"
          @click="startAdventure"
        />

        <p class="tiny-note">
          dibuat dengan sedikit keberanian oleh
          {{ config.senderName }}
        </p>
      </section>

      <!-- ======================================
           2. HALAMAN KUIS
      ======================================= -->
      <section
        v-else-if="stage === 'quiz'"
        key="quiz"
        class="screen"
      >
        <div class="section-sticker">
          question no. 01 ✿
        </div>

        <div class="big-emoji" aria-hidden="true">
          🤔
        </div>

        <p class="eyebrow">
          let's see if you can guess
        </p>

        <h2>{{ config.quizQuestion }}</h2>

        <p class="body-copy">
          Pilih satu, jangan curang dengan menebak isi
          pikiranku ya.
        </p>

        <div class="choice-list">
          <button
            v-for="(option, index) in config.quizOptions"
            :key="option.label"
            type="button"
            class="choice-button"
            :class="{
              'choice-button--selected':
                selectedAnswer === index,
            }"
            :aria-pressed="selectedAnswer === index"
            @click="chooseAnswer(index)"
          >
            <span class="choice-letter">
              {{ ['A', 'B', 'C'][index] }}
            </span>

            <span>{{ option.label }}</span>

            <span class="choice-check">
              {{ selectedAnswer === index ? '♥' : '♡' }}
            </span>
          </button>
        </div>

        <div
          v-if="selectedAnswer !== null"
          class="response-bubble"
          role="status"
        >
          {{ config.quizOptions[selectedAnswer].response }}
        </div>

        <CuteButton
          label="lanjut, aku penasaran →"
          :disabled="selectedAnswer === null"
          @click="continueFromQuiz"
        />
      </section>

      <!-- ======================================
           3. HALAMAN RAHASIA TENTANG DIA
           FOTO JPG TANPA PITA DAN SIMBOL HATI
      ======================================= -->
      <section
        v-else-if="stage === 'secrets'"
        key="secrets"
        class="screen screen--wide"
      >
        <div class="section-sticker">
          classified feelings ♡
        </div>

        <!-- FOTO PENGGANTI AMPLOP, PITA, DAN HATI -->
        <div
          v-if="photoAvailable"
          class="secret-photo-scene"
        >
          <div class="secret-photo-frame">
            <img
              :src="config.photoPath"
              :alt="`Foto ${config.recipientName}`"
              loading="eager"
              @error="handlePhotoError"
            />
          </div>
        </div>

        <div
          v-else
          class="photo-fallback"
          role="status"
        >
          <span class="photo-fallback-icon">♡</span>
          <p>Foto spesialnya belum bisa dimuat.</p>
          <p class="photo-fallback-hint">
            Periksa nama dan lokasi file foto di folder public.
          </p>
        </div>

        <p class="eyebrow">
          tap tap, open sesame
        </p>

        <h2>sedikit rahasia tentang kamu...</h2>

        <p class="body-copy">
          Ada tiga kartu kecil. Buka semuanya kalau
          kamu siap dibuat senyum-senyum sendiri.
        </p>

        <div class="secret-grid">
          <SecretCard
            v-for="(card, index) in config.secretCards"
            :key="card.title"
            :card="card"
            :opened="openedCards.includes(index)"
            @toggle="toggleCard(index)"
          />
        </div>

        <p class="tiny-note">
          {{ openedCards.length }} dari
          {{ config.secretCards.length }}
          rahasia terbuka ✨
        </p>

        <CuteButton
          label="aku sudah baca semuanya →"
          :disabled="!allCardsOpened"
          @click="go('twist')"
        />
      </section>

      <!-- ======================================
           4. HALAMAN PLOT TWIST
      ======================================= -->
      <section
        v-else-if="stage === 'twist'"
        key="twist"
        class="screen"
      >
        <div class="plot-twist-art" aria-hidden="true">
          <span class="twist-heart">♥</span>
          <span class="twist-spark">✦</span>
          <span class="twist-bow">୨୧</span>
        </div>

        <div class="section-sticker">
          plot twist incoming!
        </div>

        <p class="eyebrow">
          wait... one more thing
        </p>

        <h2>eh, jangan pergi dulu! 👀</h2>

        <p class="body-copy">
          {{ config.plotTwistMessage }}
        </p>

        <CuteButton
          label="WAIT, APA NIH?! 😳"
          @click="go('question')"
        />

        <p class="tiny-note">
          peringatan: bisa menyebabkan senyum tipis
          tanpa sadar
        </p>
      </section>

      <!-- ======================================
           5. HALAMAN PERTANYAAN PACARAN
      ======================================= -->
      <section
        v-else-if="stage === 'question'"
        key="question"
        class="screen screen--question"
      >
        <div class="question-sparkles" aria-hidden="true">
          ✦ ♡ ✧
        </div>

        <!-- FOTO DENGAN BINGKAI POLAROID -->
        <div
          v-if="photoAvailable"
          class="photo-frame"
        >
          <img
            :src="config.photoPath"
            :alt="`Foto ${config.recipientName}`"
            loading="eager"
            @error="handlePhotoError"
          />

          <span class="photo-caption">
            a little memory, maybe? ♡
          </span>
        </div>

        <div
          v-else
          class="photo-fallback"
          role="status"
        >
          <span class="photo-fallback-icon">♡</span>
          <p>Foto spesialnya belum bisa dimuat.</p>
        </div>

        <p class="eyebrow">
          from {{ config.senderName }}, with courage
        </p>

        <h2>{{ config.question }}</h2>

        <p class="body-copy">
          Aku nggak janji bakal selalu sempurna,
          tapi aku ingin mencoba mengenal dan
          menjalani ini sama kamu. ♡
        </p>

        <div class="answer-buttons">
          <CuteButton
            label="IYA, MAU!! 💗"
            @click="answerYes"
          />

          <CuteButton
            label="aku pikir-pikir dulu"
            secondary
            @click="answerThink"
          />
        </div>

        <p class="tiny-note">
          jawab dengan nyaman, ya. no pressure ♡
        </p>
      </section>

      <!-- ======================================
           6. ENDING JIKA MENJAWAB IYA
      ======================================= -->
      <section
        v-else-if="stage === 'ending-yes'"
        key="ending-yes"
        class="screen screen--ending"
      >
        <div class="celebration" aria-hidden="true">
          🎉 💗 🎀 💗 🎉
        </div>

        <div class="ending-stamp">
          OFFICIALLY CUTE
        </div>

        <p class="eyebrow">
          WAIT, IS THIS REAL?!
        </p>

        <h1>AAAAA, KAMU BILANG IYA!!</h1>

        <p class="body-copy">
          {{ config.yesMessage }}
        </p>

        <div class="love-note">
          <span class="love-note-heart">♥</span>

          <p>
            Terima kasih sudah memberi aku kesempatan.
            Ini awal cerita kecil kita,
            {{ config.recipientName }}.
            Jangan lupa traktir aku senyum hari ini,
            ya. Hehe.
          </p>

          <span class="signature">
            — {{ config.senderName }} ♡
          </span>
        </div>

        <CuteButton
          label="putar lagi petualangannya ↻"
          secondary
          @click="replay"
        />
      </section>

      <!-- ======================================
           7. ENDING JIKA INGIN BERPIKIR DULU
      ======================================= -->
      <section
        v-else-if="stage === 'ending-think'"
        key="ending-think"
        class="screen screen--ending"
      >
        <div class="big-emoji" aria-hidden="true">
          🫶
        </div>

        <div class="section-sticker">
          it's okay, really ♡
        </div>

        <h1>nggak apa-apa kok 🌷</h1>

        <p class="body-copy">
          {{ config.thinkMessage }}
        </p>

        <div class="love-note">
          <span class="love-note-heart">♡</span>

          <p>
            Aku senang sudah bisa jujur sama kamu.
            Apa pun jawabanmu nanti, makasih sudah
            membaca sampai sini.
          </p>

          <span class="signature">
            — {{ config.senderName }}
          </span>
        </div>

        <CuteButton
          label="baca lagi dari awal ↻"
          secondary
          @click="replay"
        />
      </section>
    </Transition>

    <!-- FOOTER -->
    <footer class="footer">
      <span>made with butterflies & a little courage</span>
      <span>♡</span>
    </footer>

    <!-- MUSIK LATAR -->
    <audio
      ref="audio"
      :src="config.musicPath"
      loop
      preload="none"
      @play="musicOn = true"
      @pause="musicOn = false"
    ></audio>
  </main>
</template>
```