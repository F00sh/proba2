<template>
  <section id="about" class="bg-gray-950 px-6 md:px-[30rem] py-20 min-h-screen flex flex-col justify-center w-screen">
    <h2 class="text-3xl md:text-4xl font-bold">About Me</h2>

    <p class="mt-4 text-gray-300 leading-relaxed">
      I'm Marko, a multidisciplinary designer and 3D artist with a strong background in product design, 3D visualization, and digital experiences. Over the years I’ve worked on diverse projects ranging from industrial and interior design to web interfaces, animation, and branding.
    </p>

    <p class="mt-4 text-gray-300 leading-relaxed">
      On Upwork, I collaborate with international clients to bring their ideas to life — whether through interactive web pages, detailed 3D models, or clean UI concepts. My workflow combines creativity with technical precision, always aiming to deliver work that’s both visually engaging and practical.
    </p>

    <p class="mt-4 text-gray-300 leading-relaxed">
      Explore my <a href="https://www.upwork.com/freelancers/~01a6fe8aed25d72941" target="_blank" rel="noreferrer" class="text-brand-light hover:underline">Upwork profile</a> for examples of projects, client feedback, and more details on my skills.
    </p>

    <!-- Portfolio Grid -->
    <div class="mt-12 grid gap-6 sm:grid-cols-2 md:grid-cols-3">
      <div v-for="(item, idx) in portfolio" :key="idx"
           class="rounded-2xl overflow-hidden border border-white/10 bg-gray-900 cursor-pointer hover:scale-[1.02] transition"
           @click="openLightbox(item)">
        <img :src="item.image" :alt="item.title" class="w-full h-48 object-cover" />
        <div class="p-4">
          <h3 class="font-semibold">{{ item.title }}</h3>
          <p class="mt-2 text-sm text-gray-400">{{ item.desc }}</p>
        </div>
      </div>
    </div>

    <!-- Lightbox -->
    <div v-if="lightbox" class="fixed inset-0 bg-black/80 flex items-center justify-center z-50">
      <div class="relative max-w-4xl w-full px-4">
        <button @click="lightbox = null" class="absolute -top-10 right-0 text-white text-2xl">✕</button>
        <img :src="lightbox.image" :alt="lightbox.title" class="w-full max-h-[80vh] object-contain rounded-lg shadow-lg" />
        <p class="mt-4 text-center text-gray-300">{{ lightbox.title }}</p>
      </div>
    </div>
  </section>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const portfolio = [
  {
    title: 'Product & Industrial Design',
    desc: 'Prototypes, consumer goods, and industrial objects designed for production.',
    image: '/portfolio/product-design.jpg'
  },
  {
    title: '3D Visualization & Animation',
    desc: 'High-quality 3D renders, animations, and interactive assets for various industries.',
    image: '/portfolio/3d-visualization.jpg'
  },
  {
    title: 'Web Design & Interactive Apps',
    desc: 'Nuxt/Tailwind projects, responsive landing pages, and interactive apps.',
    image: '/portfolio/web-design.jpg'
  }
]

const lightbox = ref<null | { title: string; image: string }>(null)

function openLightbox(item: { title: string; image: string }) {
  lightbox.value = item
}
</script>

<style>
html {
  scroll-behavior: smooth;
}
</style>
