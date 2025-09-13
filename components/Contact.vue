<template>
  <section id="contact" class="bg-teal-700 mx-auto w-screen px-6 md:px-[40rem] py-24 min-h-screen flex flex-col justify-center">
    <h2 class="text-3xl md:text-4xl font-bold">Contact</h2>
    <form class="mt-6 grid gap-4 md:grid-cols-2" @submit.prevent="onSubmit">
      <input v-model="form.name" type="text" placeholder="Your name"
             class="md:col-span-1 rounded-xl bg-gray-900 border border-white/10 px-4 py-3 outline-none focus:ring-2 focus:ring-brand/50" required>
      <input v-model="form.email" type="email" placeholder="Your email"
             class="md:col-span-1 rounded-xl bg-gray-900 border border-white/10 px-4 py-3 outline-none focus:ring-2 focus:ring-brand/50" required>
      <textarea v-model="form.message" rows="5" placeholder="Message"
                class="md:col-span-2 rounded-xl bg-gray-900 border border-white/10 px-4 py-3 outline-none focus:ring-2 focus:ring-brand/50"></textarea>
      <div class="md:col-span-2">
        <button type="submit" class="rounded-xl bg-brand px-6 py-3 text-sm hover:bg-brand-dark transition">
          Send
        </button>
        <span v-if="sent" class="ml-3 text-sm text-green-400">Message prepared (demo).</span>
      </div>
    </form>
  </section>
</template>

<script setup lang="ts">
import { reactive, ref } from 'vue'

const form = reactive({ name: '', email: '', message: '' })
const sent = ref(false)

function onSubmit() {
  // Demo only — wire to your backend/email service.
  sent.value = true
  setTimeout(() => { sent.value = false }, 2500)
}
</script>
