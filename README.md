# Nuxt 4 + Tailwind CSS + GSAP Starter

A minimal Nuxt 4 project pre-wired with Tailwind CSS and GSAP, including `Navbar.vue`, `Hero.vue`, `About.vue`, and `Contact.vue` components.

## Quick Start

```bash
# 1) Install dependencies
npm install

# 2) Run the dev server
npm run dev
# App runs at http://localhost:3000
```

### Notes
- Tailwind is configured via the official `@nuxtjs/tailwindcss` module (no Vite plugin needed).
- GSAP is available as `$gsap` through `plugins/gsap.client.ts`. Example usage is in `components/Hero.vue`.
- Main styling entry is `assets/css/main.css`.

Happy building! 🚀
