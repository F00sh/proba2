<template>
  <section class="relative min-h-screen flex items-center justify-center w-full overflow-hidden">
    <!-- Clouds Layer -->
    <div class="absolute inset-0 pointer-events-none overflow-hidden" ref="cloudContainer"></div>

    <div class="relative text-center px-6 flex flex-col items-center pt-20 z-10">
      <h1 ref="titleEl" class="text-[30rem] md:text-[80rem] font-extrabold tracking-tight leading-none select-none drop-shadow-[0_3px_8px_rgba(0,0,0,0.6)]">
        <span ref="hoverWordEl" class="inline-flex flex-col items-center gap-2">FOOSH</span>
      </h1>

      <p ref="subtitleEl" class="mt-10 md:mt-12 text-2xl md:text-4xl uppercase text-green-500 max-w-2xl mx-auto font-black tracking-widest">
        FOOSH is a multidisciplinary design studio crafting innovative solutions in
      </p>
      <p ref="subtitleEl" class="mt-6 md:mb-12 text-lg md:text-3xl text-green-500 max-w-2xl mx-auto tracking-widest">
        PRODUCT | APPS | INTERIOR | EXTERIOR
      </p>

      <div class="mt-10 flex items-center justify-center gap-4 mb-[12rem]">
        <a href="#about" class="rounded-xl border border-white/15 px-5 py-3 text-sm hover:bg-white/5 transition">Learn More</a>
        <a href="#contact" class="rounded-xl bg-brand px-5 py-3 text-sm hover:bg-brand-dark transition">Contact</a>
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
const cloudContainer = ref<HTMLElement | null>(null)

let spans: HTMLElement[] = []
const activeTweens = new WeakMap<HTMLElement, any>()

onMounted(() => {
  $gsap?.from(titleEl.value, { y: 40, opacity: 0, duration: 1.5, ease: 'power2.inOut' })
  $gsap?.from(subtitleEl.value, { y: 20, opacity: 0, duration: 1.5, ease: 'power2.inOut', delay: 0.2 })

  // Generate clouds in 3 depth layers
  if (cloudContainer.value) {
    const layers = [0.2, 0.5, 0.8] // speed multipliers for parallax

    for (let i = 0; i < 15; i++) {
      const img = document.createElement('img')
      img.src = new URL(`../assets/img/cloud_${(i % 3) + 1}.png`, import.meta.url).href
      img.className = 'absolute cloud'

      const size = Math.random() * 500 + 400 // 400–900px
      const top = Math.random() * 80 // vh offset
      const left = Math.random() * 100
      const opacity = Math.random() * 0.5 + 0.3

      const layerIndex = i % 3
      img.dataset.layer = layerIndex.toString()

      img.style.width = `${size}px`
      img.style.top = `${top}%`
      img.style.left = `${left}%`
      img.style.opacity = `${opacity}`
      img.style.transform = 'translate(-50%, -50%)'
      img.style.zIndex = `${layerIndex - 1}` // back to front layering

      cloudContainer.value.appendChild(img)

      // Animate drift
      $gsap.to(img, {
        x: () => $gsap.utils.random(-200, 200),
        duration: () => $gsap.utils.random(20, 40),
        ease: 'sine.inOut',
        yoyo: true,
        repeat: -1
      })
    }

    // Parallax on scroll with 3 layer depths
    window.addEventListener('scroll', () => {
      const scrollY = window.scrollY
      cloudContainer.value!.querySelectorAll('img').forEach((cloud) => {
        const layer = parseInt((cloud as HTMLElement).dataset.layer || '1')
        const speed = layers[layer]
        ;(cloud as HTMLElement).style.transform = `translate(-50%, -50%) translateY(${scrollY * speed}px)`
      })
    })
  }

  // FOOSH per-letter animations
  const word = hoverWordEl.value
  if (!word) return

  const text = word.textContent && word.textContent.trim().length > 0 ? word.textContent : 'FOOSH'
  word.textContent = ''
  spans = []

  const makeTween = (span: HTMLElement) =>
    $gsap!.to(span, {
      x: () => $gsap!.utils.random(-20, 20),
      y: () => $gsap!.utils.random(-20, 20),
      rotation: () => $gsap!.utils.random(-15, 15),
      scale: () => $gsap!.utils.random(0.85, 1.3),
      duration: () => $gsap!.utils.random(0.6, 1.2),
      ease: 'power2.inOut',
      repeat: -1,
      repeatRefresh: true
    })

  for (const ch of text.split('')) {
    const span = document.createElement('span')
    span.textContent = ch
    span.className = 'inline-block will-change-transform cursor-default drop-shadow-[0_3px_8px_rgba(0,0,0,0.6)]'
    span.style.display = 'inline-block'
    span.style.transformOrigin = '50% 50%'

    spans.push(span)
    word.appendChild(span)

    if (window.innerWidth <= 768) {
      const tween = makeTween(span)
      activeTweens.set(span, tween)
    } else {
      const enterHandler = () => {
        if (!activeTweens.has(span)) {
          const tween = makeTween(span)
          activeTweens.set(span, tween)
        }
      }
      const leaveHandler = () => {
        const t = activeTweens.get(span)
        if (t) {
          t.kill()
          activeTweens.delete(span)
        }
        $gsap?.to(span, { x: 0, y: 0, rotation: 0, scale: 1, duration: 0.6, ease: 'power2.inOut' })
      }

      span.addEventListener('pointerenter', enterHandler)
      span.addEventListener('pointerleave', leaveHandler)

      ;(span as any)._enterHandler = enterHandler
      ;(span as any)._leaveHandler = leaveHandler
    }
  }
})

onBeforeUnmount(() => {
  for (const span of spans) {
    const t = activeTweens.get(span)
    if (t) t.kill()
    if ((span as any)._enterHandler) {
      span.removeEventListener('pointerenter', (span as any)._enterHandler)
      span.removeEventListener('pointerleave', (span as any)._leaveHandler)
    }
  }
  spans = []
})
</script>

<style>
html {
  scroll-behavior: smooth;
}
</style>