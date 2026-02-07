<script setup>
import { ref, onMounted, computed } from 'vue'

// Import all images from the folder
// Using standard Vite glob import. We map the modules to their default export (the URL).
const imagesGlob = import.meta.glob('./components/img/bums/*.jpg', { eager: true })
const imagePaths = Object.values(imagesGlob).map(mod => mod.default)

// Configuration for each image.
// We define a specific transform-origin for each to zoom on a different part.
// Random values are generated for now.
const imageConfigs = imagePaths.map(() => ({
  x: Math.floor(Math.random() * 100),
  y: Math.floor(Math.random() * 100),
  scale: 5 // Zoom level
}))

const currentIndex = ref(0)
const isZoomedOut = ref(false)

const currentImage = computed(() => imagePaths[currentIndex.value])
const currentConfig = computed(() => imageConfigs[currentIndex.value])

const pickRandomImage = () => {
  if (imagePaths.length === 0) return
  currentIndex.value = Math.floor(Math.random() * imagePaths.length)
  isZoomedOut.value = false
}

const handleClick = () => {
  if (isZoomedOut.value) return // Already clicked

  isZoomedOut.value = true

  // Wait for animation to finish (0.4s) then open Wikipedia
  setTimeout(() => {
    window.open('https://wikipedia.org/wiki/Jensen_Ackles', '_blank')
  }, 500)
}

onMounted(() => {
  pickRandomImage()
})
</script>

<template>
  <main>
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
      <p class="hint" v-else>It's Jensen Ackles!</p>
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
  max-width: 70vw;
  max-height: 70vh;
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
