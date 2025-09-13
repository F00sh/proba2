<template>
  <div class="min-h-screen flex flex-col">
    <Navbar />
    <NuxtPage />
    <footer class="mt-auto py-6 text-center text-sm text-gray-400">
      Built with Nuxt 4 · Tailwind CSS · GSAP
    </footer>
  </div>
</template>

<script setup lang="ts">
import Navbar from '~/components/Navbar.vue'
</script>
