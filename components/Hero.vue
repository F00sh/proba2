<template>
  <section class="relative min-h-screen flex items-center justify-center w-screen">
    <div class="relative text-center px-6 flex flex-col items-center">
      <h1 ref="titleEl" class="text-[30rem] md:text-[80rem] font-extrabold tracking-tight leading-none select-none drop-shadow-[0_3px_8px_rgba(0,0,0,0.6)]">
        <span class="inline-flex flex-col items-center gap-2" ref="hoverWordEl">FOOSH</span>
      </h1>

      <p ref="subtitleEl" class="mt-6 text-lg md:text-xl text-green-500 max-w-2xl mx-auto">
        PRODUCT | APPS | INTERIOR | EXTERIOR
      </p>

      <div class="mt-10 flex items-center justify-center gap-4 mb-20">
        <a href="#about" class="rounded-xl border border-white/15 px-5 py-3 text-sm hover:bg-white/5 transition">Learn More</a>
        <a href="#contact" class="rounded-xl bg-brand px-5 py-3 text-sm hover:bg-brand-dark transition">Contact</a>
      </div>

      <!-- Scroll Arrows -->
      <div class="fixed top-1/2 right-4 -translate-y-1/2 z-50 flex flex-col items-center gap-4">
        <!-- Up Arrow -->
        <button v-show="!atTop" @click="scrollUp" class="transition-transform">
          <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor"
               class="w-12 h-12 text-white drop-shadow-[0_2px_6px_rgba(0,0,0,0.6)]">
            <path stroke-linecap="round" stroke-linejoin="round" d="M5 15l7-7 7 7" />
          </svg>
        </button>

        <!-- Down Arrow -->
        <button v-show="!atBottom" @click="scrollDown" class="transition-transform">
          <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor"
               class="w-12 h-12 text-white drop-shadow-[0_2px_6px_rgba(0,0,0,0.6)]">
            <path stroke-linecap="round" stroke-linejoin="round" d="M19 9l-7 7-7-7" />
          </svg>
        </button>
      </div>
    </div>
  </section>
</template>

<script setup lang="ts">
import { onMounted, onBeforeUnmount, ref } from 'vue'

const { $gsap } = useNuxtApp()

const titleEl = ref<HTMLElement | null>(null)
const subtitleEl = ref<HTMLElement | null>(null)
const hoverWordEl = ref<HTMLElement | null>(null)

let spans: HTMLElement[] = []
const activeTweens = new WeakMap<HTMLElement, any>()

const atTop = ref(true)
const atBottom = ref(false)

const sections = ['about', 'portfolio', 'contact']

function scrollDown() {
  for (const id of sections) {
    const el = document.getElementById(id)
    if (el) {
      const rect = el.getBoundingClientRect()
      if (rect.top > 1) {
        el.scrollIntoView({ behavior: 'smooth', block: 'start' })
        return
      }
    }
  }
}

function scrollUp() {
  for (let i = sections.length - 1; i >= 0; i--) {
    const el = document.getElementById(sections[i])
    if (el) {
      const rect = el.getBoundingClientRect()
      if (rect.top < -1) {
        el.scrollIntoView({ behavior: 'smooth', block: 'start' })
        return
      }
    }
  }
}

onMounted(() => {
  $gsap?.from(titleEl.value, { y: 40, opacity: 0, duration: 1.5, ease: 'power2.inOut' })
  $gsap?.from(subtitleEl.value, { y: 20, opacity: 0, duration: 1.5, ease: 'power2.inOut', delay: 0.2 })

  // Track scroll position
  window.addEventListener('scroll', () => {
    atTop.value = window.scrollY < 50
    atBottom.value = window.innerHeight + window.scrollY >= document.body.scrollHeight - 10
  })

  // FOOSH per-letter hover
  const word = hoverWordEl.value
  if (!word) return
  const text = word.textContent ?? ''
  word.textContent = ''

  const frag = document.createDocumentFragment()

  const onEnter = (span: HTMLElement) => {
    if (activeTweens.has(span)) return
    const tween = $gsap!.to(span, {
      x: () => $gsap!.utils.random(-20, 20),
      y: () => $gsap!.utils.random(-20, 20),
      rotation: () => $gsap!.utils.random(-15, 15),
      scale: () => $gsap!.utils.random(0.85, 1.3),
      duration: () => $gsap!.utils.random(0.4, 0.8),
      ease: 'power2.inOut',
      repeat: -1,
      repeatRefresh: true
    })
    activeTweens.set(span, tween)
  }

  const onLeave = (span: HTMLElement) => {
    const t = activeTweens.get(span)
    if (t) {
      t.kill()
      activeTweens.delete(span)
    }
    $gsap?.to(span, { x: 0, y: 0, rotation: 0, scale: 1, duration: 0.6, ease: 'power2.inOut' })
  }

  for (const ch of text.split('')) {
    const span = document.createElement('span')
    span.textContent = ch
    span.className = 'inline-block will-change-transform cursor-default drop-shadow-[0_3px_8px_rgba(0,0,0,0.6)]'
    span.style.display = 'inline-block'
    span.style.transformOrigin = '50% 50%'

    const enterHandler = () => onEnter(span)
    const leaveHandler = () => onLeave(span)

    span.addEventListener('pointerenter', enterHandler)
    span.addEventListener('pointerleave', leaveHandler)

    spans.push(span)
    ;(span as any)._enterHandler = enterHandler
    ;(span as any)._leaveHandler = leaveHandler

    frag.appendChild(span)
  }

  word.appendChild(frag)
})

onBeforeUnmount(() => {
  for (const span of spans) {
    const t = activeTweens.get(span)
    if (t) t.kill()
    span.removeEventListener('pointerenter', (span as any)._enterHandler)
    span.removeEventListener('pointerleave', (span as any)._leaveHandler)
  }
  spans = []
})
</script>

<style>
html {
  scroll-behavior: smooth;
}
</style>
