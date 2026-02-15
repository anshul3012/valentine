<script setup lang="ts">
import { ref } from 'vue'

const noBtn = ref<HTMLElement | null>(null)

const noX = ref(0)
const noY = ref(0)
const yesScale = ref(1)

const showConfetti = ref(false)
const showFinal = ref(false)
const hideNo = ref(false)

const confetti = ref<{ id: number; x: number; delay: number }[]>([])

let anchorIndex = 0

function anchors(btn: DOMRect) {
  const w = window.innerWidth
  const h = window.innerHeight
  const bw = btn.width
  const bh = btn.height
  const pad = 20

  return [
    // corners
    [-w / 2 + pad, -h / 2 + pad],
    [w / 2 - bw - pad, -h / 2 + pad],
    [-w / 2 + pad, h / 2 - bh - pad],
    [w / 2 - bw - pad, h / 2 - bh - pad],

    // sides
    [0, -h / 2 + pad],
    [0, h / 2 - bh - pad],
    [-w / 2 + pad, 0],
    [w / 2 - bw - pad, 0],

    // near center (but not overlapping yes)
    [-120, 80],
    [120, 80],
  ]
}

function teleportNo() {
  if (!noBtn.value) return

  const rect = noBtn.value.getBoundingClientRect()
  const list = anchors(rect)

  anchorIndex = (anchorIndex + Math.floor(Math.random() * 3) + 1) % list.length

  noX.value = list[anchorIndex]?.[0] as number
  noY.value = list[anchorIndex]?.[1] as number

  yesScale.value = Math.min(2, yesScale.value + 0.1)
}

function onYes() {
  hideNo.value = true
  showConfetti.value = true

  confetti.value = Array.from({ length: 50 }).map((_, i) => ({
    id: i,
    x: Math.random() * 100,
    delay: Math.random() * 0.6,
  }))

  setTimeout(() => {
    showConfetti.value = false
    showFinal.value = true
  }, 1500)
}
</script>

<template>
  <div class="page">
    <!-- Confetti -->
    <div v-if="showConfetti" class="confetti">
      <span
        v-for="c in confetti"
        :key="c.id"
        :style="{ left: c.x + '%', animationDelay: c.delay + 's' }"
      >
        💖
      </span>
    </div>

    <!-- Final Screen -->
    <div v-if="showFinal" class="final">
      <div class="heart">❤️</div>
      <h1>Congrats you are now his FOREVER !!</h1>
    </div>

    <!-- Main Card -->
    <div v-else class="card">
      <h1>Will you be Anshul's Valentine? 💘</h1>

      <div class="buttons">
        <button class="yes" :style="{ transform: `scale(${yesScale})` }" @click="onYes">
          Yes 💖
        </button>

        <button
          v-if="!hideNo"
          ref="noBtn"
          class="no"
          :style="{ transform: `translate(${noX}px, ${noY}px)` }"
          @mouseenter="teleportNo"
          @pointerdown.prevent="teleportNo"
          @touchstart.prevent="teleportNo"
        >
          No 💔
        </button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.page {
  min-height: 100vh;
  display: grid;
  place-items: center;
  background: linear-gradient(135deg, #ff9a9e, #fad0c4);
  overflow: hidden;
}

.card {
  background: linear-gradient(160deg, #fff, #ffe6f0);
  padding: 2.5rem;
  border-radius: 26px;
  text-align: center;
  box-shadow: 0 30px 80px rgba(0, 0, 0, 0.3);
}

.buttons {
  margin-top: 2rem;
  display: flex;
  gap: 2rem;
  justify-content: center;
  position: relative;
}

button {
  border: none;
  padding: 0.9rem 1.9rem;
  border-radius: 999px;
  font-size: 1.1rem;
  cursor: pointer;
  transition: transform 0.25s ease;
}

.yes {
  background: linear-gradient(135deg, #7cffb2, #3fd97f);
  box-shadow: 0 10px 25px rgba(63, 217, 127, 0.5);
}

.no {
  background: linear-gradient(135deg, #ff7a7a, #ff3d3d);
  box-shadow: 0 10px 25px rgba(255, 61, 61, 0.5);
  position: relative;
}

/* Confetti */
.confetti {
  position: fixed;
  inset: 0;
  pointer-events: none;
}

.confetti span {
  position: absolute;
  top: -10%;
  font-size: 1.8rem;
  animation: fall 1.5s linear forwards;
}

@keyframes fall {
  to {
    transform: translateY(120vh) rotate(720deg);
  }
}

/* Final */
.final {
  text-align: center;
  animation: fadeIn 0.6s ease;
}

.heart {
  font-size: 6rem;
  animation: pulse 1.2s infinite;
}

@keyframes pulse {
  0%,
  100% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.2);
  }
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: scale(0.9);
  }
  to {
    opacity: 1;
    transform: scale(1);
  }
}
</style>
