<template>
  <section id="portfolio" class="relative min-h-screen flex flex-col justify-center items-center px-6 py-20">
    <h2 class="text-6xl md:text-[10rem] font-extrabold uppercase tracking-widest my-[12rem]">Portfolio</h2>

    <!-- Carousel -->
    <div class="relative w-full max-w-5xl overflow-hidden mb-28">
      <div class="flex transition-transform duration-500" :style="{ transform: `translateX(-${currentIndex * 100}%)` }">
        <div v-for="(project, index) in projects" :key="index" class="min-w-full flex-shrink-0 px-4">
          <div class="rounded-2xl overflow-hidden border border-white/10 bg-gray-900 cursor-pointer"
               @click="openModal(project)">
            <img :src="project.cover" :alt="project.title" class="w-full h-64 md:h-96 object-cover" />
            <div class="p-4">
              <h3 class="font-semibold">{{ project.title }}</h3>
              <p class="mt-2 text-sm text-gray-400 line-clamp-2">{{ project.shortDesc }}</p>
            </div>
          </div>
        </div>
      </div>

      <!-- Controls -->
      <button @click="prev" class="absolute top-1/2 left-2 -translate-y-1/2 bg-black/50 rounded-full p-2">◀</button>
      <button @click="next" class="absolute top-1/2 right-2 -translate-y-1/2 bg-black/50 rounded-full p-2">▶</button>
    </div>

    <!-- Modal -->
    <div v-if="activeProject" class="fixed inset-0 bg-black/80 flex items-center justify-center z-50">
      <div class="relative max-w-4xl w-full bg-gray-900 rounded-lg shadow-lg overflow-hidden">
        <button @click="activeProject = null" class="absolute top-4 right-4 text-white text-2xl">✕</button>
        <div class="p-6">
          <h3 class="text-2xl font-bold mb-4">{{ activeProject.title }}</h3>
          <p class="text-gray-300 mb-6">{{ activeProject.longDesc }}</p>

          <!-- Inner Gallery -->
          <div class="relative w-full overflow-hidden">
            <div class="flex transition-transform duration-500" :style="{ transform: `translateX(-${innerIndex * 100}%)` }">
              <div v-for="(img, idx) in activeProject.images" :key="idx" class="min-w-full flex-shrink-0">
                <img :src="img" :alt="`${activeProject.title} image ${idx+1}`" class="w-full h-96 object-cover" />
              </div>
            </div>
            <button @click="prevInner" class="absolute top-1/2 left-2 -translate-y-1/2 bg-black/50 rounded-full p-2">◀</button>
            <button @click="nextInner" class="absolute top-1/2 right-2 -translate-y-1/2 bg-black/50 rounded-full p-2">▶</button>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const projects = [
  {
    title: 'Industrial Design Project',
    shortDesc: 'Prototype and production-ready industrial object.',
    longDesc: 'Detailed description of the industrial design process, including concept, modeling, and prototyping.',
    cover: '/portfolio/product-design.jpg',
    images: ['/portfolio/product1.jpg','/portfolio/product2.jpg','/portfolio/product3.jpg']
  },
  {
    title: '3D Visualization',
    shortDesc: 'Photorealistic renders and animations.',
    longDesc: 'Project focused on creating high quality renders and animations for architectural and product visualization.',
    cover: '/portfolio/3d-visualization.jpg',
    images: ['/portfolio/3d1.jpg','/portfolio/3d2.jpg']
  },
  {
    title: 'Web Design Project',
    shortDesc: 'Responsive Nuxt/Tailwind landing page.',
    longDesc: 'A responsive landing page built with Nuxt and TailwindCSS, optimized for performance and SEO.',
    cover: '/portfolio/web-design.jpg',
    images: ['/portfolio/web1.jpg','/portfolio/web2.jpg']
  }
]

const currentIndex = ref(0)
const activeProject = ref<any | null>(null)
const innerIndex = ref(0)

function prev() {
  currentIndex.value = (currentIndex.value - 1 + projects.length) % projects.length
}

function next() {
  currentIndex.value = (currentIndex.value + 1) % projects.length
}

function openModal(project: any) {
  activeProject.value = project
  innerIndex.value = 0
}

function prevInner() {
  if (!activeProject.value) return
  innerIndex.value = (innerIndex.value - 1 + activeProject.value.images.length) % activeProject.value.images.length
}

function nextInner() {
  if (!activeProject.value) return
  innerIndex.value = (innerIndex.value + 1) % activeProject.value.images.length
}
</script>

<style>
html {
  scroll-behavior: smooth;
}
section {
  min-height: 100vh;
}
</style>
