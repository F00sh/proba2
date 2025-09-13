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

  window.addEventListener('scroll', () => {
    atTop.value = window.scrollY < 50
    atBottom.value = window.innerHeight + window.scrollY >= document.body.scrollHeight - 10
  })

  const word = hoverWordEl.value
  if (!word) return
  const text = word.textContent ?? ''
  word.textContent = ''

  const frag = document.createDocumentFragment()

  const makeTween = (span: HTMLElement) =>
    $gsap!.to(span, {
      x: () => $gsap!.utils.random(-20, 20),
      y: () => $gsap!.utils.random(-20, 20),
      rotation: () => $gsap!.utils.random(-15, 15),
      scale: () => $gsap!.utils.random(0.85, 1.3),
      duration: () => $gsap!.utils.random(0.4, 0.8),
      ease: 'power2.inOut',
      repeat: -1,
      repeatRefresh: true
    })

  const onEnter = (span: HTMLElement) => {
    if (activeTweens.has(span)) return
    const tween = makeTween(span)
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
    span.className =
      'inline-block will-change-transform cursor-default drop-shadow-[0_3px_8px_rgba(0,0,0,0.6)]'
    span.style.display = 'inline-block'
    span.style.transformOrigin = '50% 50%'

    frag.appendChild(span)
    spans.push(span)

    if (window.innerWidth <= 768) {
      // Mobile: autoplay loop
      const tween = makeTween(span)
      activeTweens.set(span, tween)
    } else {
      // Desktop: hover only
      const enterHandler = () => onEnter(span)
      const leaveHandler = () => onLeave(span)
      span.addEventListener('pointerenter', enterHandler)
      span.addEventListener('pointerleave', leaveHandler)
      ;(span as any)._enterHandler = enterHandler
      ;(span as any)._leaveHandler = leaveHandler
    }
  }

  word.appendChild(frag)
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
