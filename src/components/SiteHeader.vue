
<script setup lang="ts">
import { ref } from 'vue'
import logo from '@/imagenes/LogoSierra.png'

type View = 'home' | 'login' | 'register' | 'booking' | 'account' | 'habitaciones'

const props = defineProps<{
  darkMode: boolean
}>()

const emit = defineEmits<{
  (event: 'toggle-theme'): void
  (event: 'navigate', view: View): void
  (event: 'section', section: string): void
}>()

const mobileMenu = ref(false)

function navigate(view: View) {
  mobileMenu.value = false
  emit('navigate', view)
}

function goToSection(section: string) {
  mobileMenu.value = false
  emit('section', section)
}
</script>

<template>
  <header class="topbar">
    <a
      class="brand"
      href="#"
      aria-label="Hotel Sierra de Santa, inicio"
      @click.prevent="navigate('home')"
    >
      <span class="brand-mark">
        <img :src="logo" alt="Logotipo de Sierra de Santa" />
      </span>

      <span class="brand-copy">
        <strong>HOTEL SIERRA DE SANTA</strong>
        <small>Porque las historias comienzan al pie de la montaña.</small>
      </span>
    </a>

    <button
      class="menu-toggle"
      type="button"
      :aria-expanded="mobileMenu"
      aria-label="Abrir o cerrar menú"
      @click="mobileMenu = !mobileMenu"
    >
      {{ mobileMenu ? '✕' : '☰' }}
    </button>

    <nav :class="{ 'nav-open': mobileMenu }">
      <a href="#experiencias" @click.prevent="goToSection('experiencias')">
        Experiencias
      </a>

      <a href="#HabitacionesView.vue" @click.prevent="navigate('habitaciones')">
        Habitaciones
      </a>

      <a href="#opiniones" @click.prevent="goToSection('opiniones')">
        Opiniones
      </a>

      <button
        class="theme-toggle"
        type="button"
        :aria-label="props.darkMode ? 'Activar modo claro' : 'Activar modo oscuro'"
        @click="emit('toggle-theme')"
      >
        {{ props.darkMode ? '☼' : '☾' }}
      </button>

      <button class="nav-login" type="button" @click="navigate('login')">
        Iniciar sesión
      </button>
    </nav>
  </header>
</template>
