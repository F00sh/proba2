<template>
  <header ref="header" class="fixed top-0 left-0 w-full z-50 transition-transform duration-300 bg-yellow-400 border-b border-purple-700">
    <nav class="mx-auto max-w-6xl px-4 h-16 flex items-center justify-between">
      <a href="/" class="font-black tracking-wide text-purple-700">FOOSH</a>
      <ul class="hidden md:flex items-center gap-6 text-sm text-purple-700">
        <li><a href="#about" class="hover:text-purple-900">About</a></li>
        <li><a href="#portfolio" class="hover:text-purple-900">Portfolio</a></li>
        <li><a href="#contact" class="hover:text-purple-900">Contact</a></li>
      </ul>
      <button @click="toggleMenu" class="md:hidden inline-flex items-center justify-center rounded-lg border border-purple-700 px-3 py-1 text-purple-700 hover:bg-purple-200">
        ☰
      </button>
    </nav>

    <!-- Mobile Menu -->
    <div v-show="menuOpen" class="md:hidden bg-green-500 border-t border-purple-700 px-4 py-4">
      <ul class="flex flex-col gap-4 text-gray-100 items-center">
        <li><a href="#about" class="hover:text-purple-900" @click="closeMenu">About</a></li>
        <li><a href="#portfolio" class="hover:text-purple-900" @click="closeMenu">Portfolio</a></li>
        <li><a href="#contact" class="hover:text-purple-900" @click="closeMenu">Contact</a></li>
      </ul>
    </div>
  </header>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'

const header = ref<HTMLElement | null>(null)
const lastScroll = ref(0)
const menuOpen = ref(false)

function toggleMenu() {
  menuOpen.value = !menuOpen.value
}

function closeMenu() {
  menuOpen.value = false
}

onMounted(() => {
  window.addEventListener('scroll', () => {
    const currentScroll = window.scrollY
    if (!header.value) return
    if (currentScroll > lastScroll.value && currentScroll > 80) {
      // scrolling down
      header.value.style.transform = 'translateY(-100%)'
    } else {
      // scrolling up
      header.value.style.transform = 'translateY(0)'
    }
    lastScroll.value = currentScroll
  })
})
</script>
