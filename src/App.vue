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
  '08.jpg': { x: 65, y: 85 },
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

const currentImage = computed(() => imageData[currentIndex.value]?.path)
const currentConfig = computed(() => imageData[currentIndex.value]?.config)

const pickRandomImage = () => {
  if (imageData.length === 0) return
  currentIndex.value = Math.floor(Math.random() * imageData.length)
  isZoomedOut.value = false
}

const handleClick = () => {
  if (isZoomedOut.value) return // Already clicked

  isZoomedOut.value = true

  // Wait for animation to finish (3s), then open Wikipedia
  // setTimeout(() => {
  //   window.open('https://wikipedia.org/wiki/Jensen_Ackles', '_blank')
  // }, 3000)
}

onMounted(() => {
  pickRandomImage()
})
</script>

<template>
  <main>
    <button class="retry-btn" @click="pickRandomImage">I need to try again</button>
    <h1>Guess whose bum?</h1>
    <div class="game-container" v-if="currentImage">
      <div
        class="image-wrapper"
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
      <p class="hint" v-if="!isZoomedOut">Click the image to reveal!</p>
      <p class="hint" v-else>OMG! It's Jensen Ackles bum!</p>
    </div>
    <div v-else>
      <p>No images found in ./components/img/bums/</p>
    </div>
  </main>
</template>

<style scoped>
main {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
  background-color: #222;
  color: white;
  font-family: sans-serif;
  text-align: center;
  position: relative; /* Needed for absolute positioning of the button */
}

.retry-btn {
  position: absolute;
  top: 20px;
  right: 20px;
  padding: 10px 20px;
  font-size: 1rem;
  cursor: pointer;
  background-color: #fff;
  color: #222;
  border: none;
  border-radius: 5px;
  box-shadow: 0 2px 5px rgba(0,0,0,0.2);
  transition: background-color 0.2s;
  z-index: 10;
}

.retry-btn:hover {
  background-color: #eee;
}

h1 {
  margin-bottom: 2rem;
  font-size: 2rem;
}

.game-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;
}

.image-wrapper {
  width: 300px;
  height: 300px;
  overflow: hidden;
  border: 4px solid #fff;
  border-radius: 12px;
  cursor: pointer;
  position: relative;
  background-color: #000;
  box-shadow: 0 4px 15px rgba(0,0,0,0.5);
  transition: all 0.4s ease; /* Added transition for wrapper size changes */
}

.image-wrapper.zoomed-out {
  width: auto;
  height: auto;
  max-width: 90vw;
  max-height: 80vh;
  overflow: visible;
}

img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: transform 0.4s cubic-bezier(0.25, 0.46, 0.45, 0.94);
  /* transform and transform-origin are set inline */
  display: block;
}

.image-wrapper.zoomed-out img {
  width: auto;
  height: auto;
  max-width: 100%;
  max-height: 100%;
  object-fit: contain;
}

.hint {
  font-size: 1.2rem;
  opacity: 0.8;
  min-height: 1.5em;
}
</style>
