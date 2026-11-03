
<script setup lang="ts">
import logo from '@/imagenes/LogoSierra.png'

type View = 'home' | 'login' | 'register' | 'booking' | 'account' | 'habitaciones'

const emit = defineEmits<{
  (event: 'navigate', view: View): void
  (event: 'section', section: string): void
}>()
</script>

<template>
  <footer class="footer">
    <div class="footer-main">
      <a class="brand brand-footer" href="#" @click.prevent="emit('navigate', 'home')">
        <span class="brand-mark">
          <img :src="logo" alt="Logotipo de Sierra de Santa" />
        </span>

        <span class="brand-copy">
          <strong>SIERRA DE SANTA</strong>
          <small>HOTEL · NATURALEZA · DESCANSO</small>
        </span>
      </a>

      <p>
        Contactanos: 811 ### 254## <br />
        hotelss2026@gmail.com
      </p>

      <div class="footer-links">
        <a href="#experiencias" @click.prevent="emit('section', 'experiencias')">
          Experiencias
        </a>

        <a href="#HabitacionesView.vue" @click.prevent="emit('navigate', 'habitaciones')">
          Habitaciones
        </a>

        <button type="button" @click="emit('navigate', 'login')">
          Mi cuenta
        </button>
      </div>
    </div>

    <div class="footer-bottom">
      <span>© 2026 Hotel Sierra de Santa</span>
      <span>UN REFUGIO PARA VOLVER A TI</span>
    </div>
  </footer>
</template>
