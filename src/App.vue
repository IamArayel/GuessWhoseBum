<script setup>
import { ref, onMounted, computed } from 'vue'

// Import all images from the folder
// Using standard Vite glob import.
const imagesGlob = import.meta.glob('./components/img/bums/*.jpg', { eager: true })

// Configuration manuelle des points de focus pour chaque image.
// Les clés correspondent aux noms de fichiers. Les valeurs sont en pourcentage (0-100).
// x:0, y:0 est en haut à gauche, x:100, y:100 est en bas à droite.
// Centrer le zoom sur la partie "intéressante" de l'image.
const focusPoints = {
  '01.jpg': { x: 70, y: 65 },
  '02.jpg': { x: 85, y: 65 },
  '03.jpg': { x: 85, y: 85 },
  '04.jpg': { x: 80, y: 80 },
  '05.jpg': { x: 45, y: 70 },
  '06.jpg': { x: 97, y: 20 },
  '07.jpg': { x: 30, y: 60 },
  '08.jpg': { x: 75, y: 90 },
  '09.jpg': { x: 30, y: 70 },
  '10.jpg': { x: 80, y: 85 },
  '11.jpg': { x: 25, y: 95 },
  '12.jpg': { x: 30, y: 95 },
  '13.jpg': { x: 50, y: 90 },
  '14.jpg': { x: 25, y: 60 },
  '15.jpg': { x: 50, y: 80 },
  '16.jpg': { x: 50, y: 60 },
  '17.jpg': { x: 45, y: 85 },
  '18.jpg': { x: 35, y: 50 },
  '19.jpg': { x: 25, y: 55 },
  '20.jpg': { x: 50, y: 95 },
  '21.jpg': { x: 50, y: 85 },
  '22.jpg': { x: 55, y: 60 },
  '23.jpg': { x: 70, y: 65 },
  '24.jpg': { x: 55, y: 50 },
  '25.jpg': { x: 45, y: 75 },
  '26.jpg': { x: 25, y: 50 },
  '27.jpg': { x: 80, y: 50 },
  '28.jpg': { x: 60, y: 80 },
  '29.jpg': { x: 50, y: 55 },
  '30.jpg': { x: 55, y: 50 },
  '31.jpg': { x: 45, y: 85 },
  '32.jpg': { x: 40, y: 55 },
  '33.jpg': { x: 45, y: 60 },
  '34.jpg': { x: 40, y: 20 },
  '35.jpg': { x: 65, y: 45 },
}

// Transformation de la liste des images pour inclure la config
const imageData = Object.entries(imagesGlob).map(([key, mod]) => {
  const filename = key.split('/').pop()
  // Utilise la config définie ou génère des valeurs aléatoires
  const focus = focusPoints[filename] || {
    x: Math.floor(Math.random() * 100),
    y: Math.floor(Math.random() * 100)
  }

  return {
    path: mod.default,
    config: {
      x: focus.x,
      y: focus.y,
      scale: 5 // Zoom level
    }
  }
})

const currentIndex = ref(0)
const isZoomedOut = ref(false)
const isDarkMode = ref(false)
const isRedirectEnabled = ref(true) // Default to enabled

const currentImage = computed(() => imageData[currentIndex.value]?.path)
const currentConfig = computed(() => imageData[currentIndex.value]?.config)

const pickRandomImage = () => {
  if (imageData.length === 0) return
  currentIndex.value = Math.floor(Math.random() * imageData.length)
  isZoomedOut.value = false
}

const handleClick = () => {
  if (isZoomedOut.value) return
  isZoomedOut.value = true

  // Only redirect if enabled
  if (isRedirectEnabled.value) {
    setTimeout(() => {
      window.open('https://wikipedia.org/wiki/Jensen_Ackles', '_blank')
    }, 3000)
  }
}

const toggleTheme = () => {
  isDarkMode.value = !isDarkMode.value
  document.documentElement.setAttribute('data-theme', isDarkMode.value ? 'dark' : 'light')
}

onMounted(() => {
  // Detect system preference
  if (window.matchMedia && window.matchMedia('(prefers-color-scheme: dark)').matches) {
    isDarkMode.value = true
    document.documentElement.setAttribute('data-theme', 'dark')
  } else {
    document.documentElement.setAttribute('data-theme', 'light')
  }
  pickRandomImage()
})
</script>

<template>
  <main>
    <!-- Theme Toggle -->
    <button class="theme-toggle neu-btn-icon" @click="toggleTheme" title="Toggle Theme">
      <span v-if="isDarkMode">☀️</span>
      <span v-else>🌙</span>
    </button>

    <div class="content-wrapper">
      <h1 class="title">Guess whose bum?</h1>

      <div class="game-card neu-card" v-if="currentImage">
        <div
          class="image-frame neu-inset"
          @click="handleClick"
          :class="{ 'zoomed-out': isZoomedOut }"
        >
          <img
            :src="currentImage"
            alt="Guess whose bum"
            :style="{
              transformOrigin: `${currentConfig.x}% ${currentConfig.y}%`,
              transform: isZoomedOut ? 'scale(1)' : `scale(${currentConfig.scale})`
            }"
          />
        </div>

        <div class="info-area">
          <p class="hint" v-if="!isZoomedOut">Click to reveal</p>
          <p class="reveal-text" v-else>OMG! It's Jensen Ackles bum!</p>
        </div>

        <button class="retry-btn neu-btn" @click="pickRandomImage">
          Try another one
        </button>

        <!-- Neumorphic Checkbox -->
        <label class="neu-checkbox-label">
          <input type="checkbox" v-model="isRedirectEnabled" />
          <div class="neu-checkbox-box">
            <svg viewBox="0 0 24 24" class="checkmark">
              <path d="M5 12l5 5L20 7" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"/>
            </svg>
          </div>
          <span class="checkbox-text">Enable Wikipedia Redirect</span>
        </label>

      </div>

      <div v-else class="neu-card">
        <p>No images found.</p>
      </div>
    </div>
  </main>
</template>

<style>
/* Global Variables for Neumorphism */
:root {
  --bg-color: #e0e5ec;
  --text-color: #4d4d4d;
  --shadow-light: #ffffff;
  --shadow-dark: #a3b1c6;
  --accent-color: #6d5dfc;
  --btn-text: #4d4d4d;
}

[data-theme="dark"] {
  --bg-color: #292d3e;
  --text-color: #e6e6e6;
  --shadow-light: #35394f;
  --shadow-dark: #1d212d;
  --accent-color: #8278f7;
  --btn-text: #e6e6e6;
}

body {
  background-color: var(--bg-color);
  color: var(--text-color);
  transition: background-color 0.3s ease, color 0.3s ease;
  margin: 0;
  font-family: 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
}
</style>

<style scoped>
main {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  position: relative;
  padding: 20px;
}

.content-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 2rem;
  width: 100%;
  max-width: 500px;
}

.title {
  font-size: 2.5rem;
  font-weight: 700;
  margin: 0;
  color: var(--text-color);
  text-shadow: 2px 2px 4px var(--shadow-dark), -2px -2px 4px var(--shadow-light);
}

/* Neumorphic Card */
.neu-card {
  background: var(--bg-color);
  border-radius: 30px;
  box-shadow: 12px 12px 24px var(--shadow-dark), -12px -12px 24px var(--shadow-light);
  padding: 2rem;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1.5rem;
  width: 100%;
  box-sizing: border-box;
  transition: all 0.3s ease;
}

/* Image Frame (Inset Shadow) */
.image-frame {
  width: 300px;
  height: 300px;
  border-radius: 20px;
  overflow: hidden;
  cursor: pointer;
  position: relative;
  border: 5px solid var(--bg-color); /* Creates spacing for the inset shadow */
  transition: all 0.5s cubic-bezier(0.25, 0.8, 0.25, 1);
}

.neu-inset {
  box-shadow: inset 8px 8px 16px var(--shadow-dark), inset -8px -8px 16px var(--shadow-light);
}

.image-frame.zoomed-out {
  width: 100%;
  height: auto;
  aspect-ratio: 1/1; /* Maintain square or adjust as needed */
  box-shadow: none; /* Remove inset shadow when revealed if desired, or keep it */
  border-radius: 10px;
}

img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  display: block;
  transition: transform 0.5s cubic-bezier(0.25, 0.46, 0.45, 0.94);
}

.image-frame.zoomed-out img {
  object-fit: contain;
}

/* Text Info */
.info-area {
  height: 2rem; /* Fixed height to prevent jumping */
  display: flex;
  align-items: center;
  justify-content: center;
}

.hint {
  font-size: 1rem;
  opacity: 0.7;
  margin: 0;
}

.reveal-text {
  font-size: 1.2rem;
  font-weight: bold;
  color: var(--accent-color);
  margin: 0;
  animation: fadeIn 0.5s ease;
}

/* Neumorphic Buttons */
.neu-btn {
  padding: 12px 30px;
  border: none;
  border-radius: 50px;
  background: var(--bg-color);
  color: var(--btn-text);
  font-weight: 600;
  font-size: 1rem;
  cursor: pointer;
  box-shadow: 6px 6px 12px var(--shadow-dark), -6px -6px 12px var(--shadow-light);
  transition: all 0.2s ease;
  outline: none;
}

.neu-btn:active {
  box-shadow: inset 4px 4px 8px var(--shadow-dark), inset -4px -4px 8px var(--shadow-light);
  transform: translateY(1px);
}

.neu-btn:hover {
  color: var(--accent-color);
}

/* Theme Toggle Button */
.theme-toggle {
  position: absolute;
  top: 20px;
  right: 20px;
  width: 50px;
  height: 50px;
  border-radius: 50%;
  border: none;
  background: var(--bg-color);
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 1.5rem;
  box-shadow: 5px 5px 10px var(--shadow-dark), -5px -5px 10px var(--shadow-light);
  transition: all 0.3s ease;
  z-index: 100;
}

.theme-toggle:active {
  box-shadow: inset 3px 3px 6px var(--shadow-dark), inset -3px -3px 6px var(--shadow-light);
}

/* Neumorphic Checkbox */
.neu-checkbox-label {
  display: flex;
  align-items: center;
  gap: 10px;
  cursor: pointer;
  user-select: none;
  margin-top: 10px;
}

.neu-checkbox-label input {
  display: none; /* Hide default checkbox */
}

.neu-checkbox-box {
  width: 24px;
  height: 24px;
  border-radius: 6px;
  background: var(--bg-color);
  box-shadow: 4px 4px 8px var(--shadow-dark), -4px -4px 8px var(--shadow-light);
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.2s ease;
}

/* Checked State: Inset Shadow */
.neu-checkbox-label input:checked + .neu-checkbox-box {
  box-shadow: inset 3px 3px 6px var(--shadow-dark), inset -3px -3px 6px var(--shadow-light);
}

.checkmark {
  width: 16px;
  height: 16px;
  color: var(--accent-color);
  opacity: 0;
  transform: scale(0.5);
  transition: all 0.2s cubic-bezier(0.5, 1.6, 0.4, 0.7);
}

.neu-checkbox-label input:checked + .neu-checkbox-box .checkmark {
  opacity: 1;
  transform: scale(1);
}

.checkbox-text {
  font-size: 0.9rem;
  color: var(--text-color);
  opacity: 0.8;
}

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(10px); }
  to { opacity: 1; transform: translateY(0); }
}

/* Responsive adjustments */
@media (max-width: 400px) {
  .image-frame {
    width: 250px;
    height: 250px;
  }
  .title {
    font-size: 2rem;
  }
}
</style>
