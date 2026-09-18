<script setup lang="ts">
import { onMounted, onUnmounted, ref } from 'vue'
import mapboxgl from 'mapbox-gl'

mapboxgl.accessToken = import.meta.env.VITE_MAPBOX_TOKEN

const mapContainer = ref<HTMLElement | null>(null)

let map: mapboxgl.Map | null = null

onMounted(() => {
  // Initialize the map
  if (!mapContainer.value) {
    return
  }

  map = new mapboxgl.Map({
    container: mapContainer.value,
    style: 'mapbox://styles/ancientpixel/cmu6xpukq002x01qt6w17aicq',
    projection: 'globe',
    center: [0, 40],
    zoom: 2.5,
  })

  map.on('style.load', () => {
    map?.setFog({})
  })

  map.on('load', () => {
    rotateGlobe()
  })
})

// Remove map on unmount
onUnmounted(() => {
  map?.remove()
  map = null
  if (rotationAnimation) {
    cancelAnimationFrame(rotationAnimation)
  }
})

// Globe rotation animation
let rotationAnimation: number | null = null

const rotateGlobe = () => {
  if (!map) {
    return
  }

  const center = map.getCenter()

  map.setCenter([center.lng + 0.04, center.lat])

  rotationAnimation = requestAnimationFrame(rotateGlobe)
}
</script>

<template>
  <main class="landing">
    <div ref="mapContainer" class="globe" />
  </main>
</template>

<style scoped lang="scss">
.landing {
  width: 100%;
  height: 100vh;
}

.globe {
  width: 100%;
  height: 100%;
}
</style>
