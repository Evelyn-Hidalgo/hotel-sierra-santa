```vue
<script setup lang="ts">
import SiteHeader from '@/components/SiteHeader.vue'
import SiteFooter from '@/components/SiteFooter.vue'

import hab2 from '@/imagenes/habitacion2.jfif'
import hab5 from '@/imagenes/paquete_5.jfif'
import hab4 from '@/imagenes/paquete_1.jfif'
import habp2 from '@/imagenes/paquete_3.jfif'
import hab1 from '@/imagenes/pac_2.jfif'
import hab6 from '@/imagenes/paquete_4.webp'
import hab3 from '@/imagenes/paquete_6.jpg'

type View =
  | 'home'
  | 'login'
  | 'register'
  | 'booking'
  | 'account'
  | 'habitaciones'

const props = defineProps<{
  darkMode: boolean
}>()

const emit = defineEmits<{
  (event: 'toggle-theme'): void
  (event: 'navigate', view: View): void
  (event: 'section', section: string): void
}>()

interface Habitacion {
  id: number
  nombre: string
  tipo: string
  capacidad: number
  precio: number
  descripcion: string
  imagen: string
  caracteristicas: string[]
  premium?: boolean
}

const habitaciones: Habitacion[] = [
  {
    id: 1,
    nombre: 'Habitación Individual',
    tipo: 'Individual',
    capacidad: 1,
    precio: 850,
    descripcion:
      'Un espacio acogedor para disfrutar de una estancia tranquila y cómoda.',
    imagen: hab1,
    caracteristicas: [
      '1 cama individual',
      'Baño privado',
      'Ambiente tranquilo'
    ]
  },
  {
    id: 2,
    nombre: 'Habitación Doble',
    tipo: 'Doble',
    capacidad: 2,
    precio: 1200,
    descripcion:
      'Ideal para parejas o viajeros que desean descansar rodeados de naturaleza.',
    imagen: hab2,
    caracteristicas: [
      '1 cama matrimonial',
      'Baño privado',
      'Espacio de descanso'
    ]
  },
  {
    id: 3,
    nombre: 'Habitación Triple',
    tipo: 'Triple',
    capacidad: 3,
    precio: 1550,
    descripcion:
      'Una opción práctica para compartir la experiencia con amigos o familia.',
    imagen: hab3,
    caracteristicas: [
      'Capacidad para 3 personas',
      'Baño privado',
      'Espacio compartido'
    ]
  },
  {
    id: 4,
    nombre: 'Habitación Familiar',
    tipo: 'Familiar',
    capacidad: 4,
    precio: 1890,
    descripcion:
      'Un espacio pensado para disfrutar de una escapada familiar en la sierra.',
    imagen: hab4,
    caracteristicas: [
      'Capacidad para 4 personas',
      'Área de descanso',
      'Baño privado'
    ]
  },
  {
    id: 5,
    nombre: 'Habitación Grupal',
    tipo: 'Grupal',
    capacidad: 5,
    precio: 2300,
    descripcion:
      'Una alternativa cómoda para grupos que desean compartir su estancia.',
    imagen: hab5,
    caracteristicas: [
      'Capacidad para 5 personas',
      'Espacio compartido',
      'Baño privado'
    ]
  },
  {
    id: 6,
    nombre: 'Habitación Grupal Plus',
    tipo: 'Grupal',
    capacidad: 6,
    precio: 2700,
    descripcion:
      'Más espacio para disfrutar de la naturaleza en compañía de tus personas favoritas.',
    imagen: hab6,
    caracteristicas: [
      'Capacidad para 6 personas',
      'Área de descanso',
      'Baño privado'
    ]
  },
  {
    id: 7,
    nombre: 'Suite Premium',
    tipo: 'Premium',
    capacidad: 2,
    precio: 2490,
    descripcion:
      'Una experiencia especial para dos, con mayor comodidad y un ambiente exclusivo.',
    imagen:habp2,
    caracteristicas: [
      'Capacidad para 2 personas',
      'Habitación premium',
      'Experiencia para dos'
    ],
    premium: true
  }
]

const formatoPrecio = (precio: number) =>
  new Intl.NumberFormat('es-MX', {
    style: 'currency',
    currency: 'MXN',
    maximumFractionDigits: 0
  }).format(precio)

function reservarHabitacion(habitacion: Habitacion) {

  sessionStorage.setItem(
    'habitacionSeleccionada',
    JSON.stringify(habitacion)
  )

  emit('navigate', 'booking')
}
</script>

<template>
  <div class="catalog-page" :class="{ 'dark-mode': props.darkMode }">
    <SiteHeader
      :dark-mode="props.darkMode"
      @toggle-theme="emit('toggle-theme')"
      @navigate="emit('navigate', $event)"
      @section="emit('section', $event)"
    />

    <main>
      <!-- Portada del catálogo -->
      <section class="catalog-hero">
        <div class="hero-content">
          <span class="eyebrow">HOTEL SIERRA DE SANTA</span>

          <h1>
            Encuentra tu lugar<br />
            <em>para descansar.</em>
          </h1>

          <p>
            Descubre nuestros espacios y elige la habitación ideal
            para disfrutar de una estancia entre montañas.
          </p>

          <a href="#habitaciones-catalogo" class="hero-button">
            Explorar habitaciones
            <span>↓</span>
          </a>
        </div>

        <div class="hero-decoration" aria-hidden="true">
          <span class="hero-ring ring-one"></span>
          <span class="hero-ring ring-two"></span>
          <span class="hero-ring ring-three"></span>

          <span class="hero-decoration-text">
            TU REFUGIO<br />
            EN LA SIERRA
          </span>
        </div>
      </section>

      <!-- Introducción -->
      <section class="catalog-intro" id="habitaciones-catalogo">
        <div>
          <span class="eyebrow">NUESTROS ESPACIOS</span>
          <h2>Catálogo de habitaciones</h2>
        </div>

        <p>
          Desde espacios individuales hasta habitaciones para grupos,
          encuentra la opción que mejor se adapte a tu viaje.
        </p>
      </section>

      <!-- Catálogo -->
      <section class="catalog-container">
        <div class="catalog-grid">
          <article
            v-for="habitacion in habitaciones"
            :key="habitacion.id"
            class="room-card"
            :class="{ 'premium-card': habitacion.premium }"
          >
            <div class="room-image">
              <img
                :src="habitacion.imagen"
                :alt="habitacion.nombre"
                loading="lazy"
              />

              <span
                class="room-badge"
                :class="{ 'premium-badge': habitacion.premium }"
              >
                {{ habitacion.premium ? 'EXPERIENCIA PREMIUM' : habitacion.tipo }}
              </span>
            </div>

            <div class="room-info">
              <div class="room-heading">
                <h3>{{ habitacion.nombre }}</h3>

                <span class="room-capacity">
                  {{ habitacion.capacidad }}
                  {{ habitacion.capacidad === 1 ? 'persona' : 'personas' }}
                </span>
              </div>

              <p class="room-description">
                {{ habitacion.descripcion }}
              </p>

              <div class="room-separator"></div>

              <ul class="room-features">
                <li
                  v-for="caracteristica in habitacion.caracteristicas"
                  :key="caracteristica"
                >
                  <span class="check">✓</span>
                  {{ caracteristica }}
                </li>
              </ul>

              <div class="room-footer">
                <div class="price-container">
                  <span class="price-label">Desde</span>

                  <strong>{{ formatoPrecio(habitacion.precio) }}</strong>

                  <span class="price-period">por noche</span>
                </div>

              </div>
            </div>
          </article>
        </div>

        <p class="catalog-disclaimer">
          Los precios y las características son ilustrativos y deben
          confirmarse con las tarifas y servicios reales del hotel.
        </p>
      </section>

      <!-- Invitación final -->
      <section class="catalog-cta">
        <div>
          <span class="eyebrow">TU PRÓXIMA ESCAPADA</span>

          <h2>El descanso que mereces.</h2>

          <p>
            Elige tu habitación y comienza a planear tu próxima visita
            a la Sierra de Santa.
          </p>
        </div>
      </section>
    </main>

    <SiteFooter
      @navigate="emit('navigate', $event)"
      @section="emit('section', $event)"
    />
  </div>
</template>

<style scoped>
.catalog-page {
  --background: #f8f6ef;
  --surface: #ffffff;
  --text: #263b33;
  --muted: #77847b;
  --green: #125f50;
  --blue: #123f63;
  --coral: #ef6f6c;
  --border: #e6e9e1;

  min-height: 100vh;
  color: var(--text);
  background: var(--background);
  transition: background 0.25s, color 0.25s;
}

.catalog-page.dark-mode {
  --background: #131118;
  --surface: #191820;
  --text: #EAE9FC;
  --muted: #aaa9b8;
  --green: #A1EDDE;
  --blue: #202b39;
  --coral: #931210;
  --border: #383640;
}

/* ===== MODO OSCURO: CATÁLOGO DE HABITACIONES ===== */

/* Textos principales */
.catalog-page.dark-mode {
  color: var(--ink, #EAE9FC);
  background: var(--background);
}

.dark-mode .catalog-intro h2,
.dark-mode .room-heading h3,
.dark-mode .catalog-cta h2 {
  color: #EAE9FC;
}

/* Textos secundarios */
.dark-mode .catalog-intro > p,
.dark-mode .room-description,
.dark-mode .catalog-disclaimer,
.dark-mode .price-label,
.dark-mode .price-period,
.dark-mode .catalog-cta p {
  color: #aaa9b8;
}

/* Tarjetas */
.dark-mode .room-card {
  background: #191820;
  border-color: #383640;
  color: #EAE9FC;
}

/* Separadores */
.dark-mode .room-separator,
.dark-mode .room-footer {
  border-color: #383640;
}

/* Capacidad */
.dark-mode .room-capacity {
  background: #202b39;
  color: #A1EDDE;
}

/* Características */
.dark-mode .room-features li {
  color: #EAE9FC;
}

.dark-mode .check {
  color: #A1EDDE;
}

/* Precios */
.dark-mode .price-container strong {
  color: #A1EDDE;
}

/* Etiquetas de habitaciones */
.dark-mode .room-badge {
  background: #202b39;
  color: #A1EDDE;
}

.dark-mode .premium-badge {
  background: #931210;
  color: #EAE9FC;
}

/* Habitación premium */
.dark-mode .premium-card {
  border-color: #931210;
}

/* Invitación final */
.dark-mode .catalog-cta {
  background: #1b1a21;
}

/* Efectos de las tarjetas */
.dark-mode .room-card:hover {
  box-shadow: 0 12px 28px rgba(0, 0, 0, 0.3);
}

/* Portada: conservar contraste */
.dark-mode .catalog-hero {
  background: linear-gradient(115deg, #202b39, #131118);
}

/* Portada */

.catalog-hero {
  position: relative;
  display: flex;
  align-items: center;
  min-height: 330px;
  padding: 55px max(7%, calc((100% - 1160px) / 2));
  overflow: hidden;
  color: #ffffff;
  background: linear-gradient(115deg, #125f50, #123f63);
}

.hero-content {
  position: relative;
  z-index: 1;
  max-width: 650px;
}

.eyebrow {
  display: block;
  margin-bottom: 13px;
  color: var(--green);
  font-size: 10px;
  font-weight: 800;
  letter-spacing: 2.5px;
}

.catalog-hero .eyebrow {
  color: #a1edde;
}

.catalog-hero h1 {
  margin: 0;
  font-size: clamp(35px, 5vw, 55px);
  font-weight: 600;
  line-height: 1.12;
  letter-spacing: -1.5px;
}

.catalog-hero h1 em {
  color: #a1edde;
  font-family: Georgia, serif;
  font-weight: 400;
}

.catalog-hero p {
  max-width: 460px;
  margin: 19px 0 25px;
  color: #e0ece7;
  font-size: 13px;
  line-height: 1.9;
}

.hero-button {
  display: inline-flex;
  align-items: center;
  gap: 20px;
  padding-bottom: 7px;
  border-bottom: 1px solid #a1edde;
  color: #ffffff;
  font-size: 12px;
  text-decoration: none;
}

.hero-button span {
  color: #a1edde;
  font-size: 17px;
}

.hero-decoration {
  position: absolute;
  top: 50%;
  right: 10%;
  display: grid;
  place-items: center;
  width: 210px;
  height: 210px;
  transform: translateY(-50%);
}

.hero-ring {
  position: absolute;
  border: 1px solid rgba(161, 237, 222, 0.3);
  border-radius: 50%;
}

.ring-one {
  inset: 0;
}

.ring-two {
  inset: 18px;
}

.ring-three {
  inset: 36px;
}

.hero-decoration-text {
  color: #d8eee6;
  font-size: 10px;
  line-height: 2;
  letter-spacing: 2px;
  text-align: center;
}

/* Encabezado del catálogo */

.catalog-intro {
  display: flex;
  align-items: end;
  justify-content: space-between;
  gap: 30px;
  max-width: 1200px;
  margin: 0 auto;
  padding: 60px 35px 30px;
  scroll-margin-top: 25px;
}

.catalog-intro h2 {
  margin: 0;
  font-family: Georgia, serif;
  font-size: clamp(27px, 4vw, 36px);
  font-weight: 500;
}

.catalog-intro > p {
  max-width: 390px;
  margin: 0 0 3px;
  color: var(--muted);
  font-size: 12px;
  line-height: 1.9;
}

/* Tarjetas */

.catalog-container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 35px 55px;
}

.catalog-grid {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 24px;
}

.room-card {
  min-width: 0;
  overflow: hidden;
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: 4px;
  transition: transform 0.25s, box-shadow 0.25s;
}

.room-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 12px 28px rgba(18, 63, 50, 0.09);
}

.room-image {
  position: relative;
  height: 195px;
  overflow: hidden;
  background: #e4e9df;
}

.room-image img {
  display: block;
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: transform 0.4s;
}

.room-card:hover .room-image img {
  transform: scale(1.04);
}

.room-badge {
  position: absolute;
  top: 13px;
  left: 13px;
  padding: 7px 10px;
  color: #125f50;
  background: #f8f6ef;
  font-size: 9px;
  font-weight: 800;
  letter-spacing: 0.5px;
}

.premium-badge {
  color: #ffffff;
  background: #123f63;
}

.premium-card {
  border-color: rgba(239, 111, 108, 0.55);
}

.room-info {
  padding: 19px;
}

.room-heading {
  display: flex;
  align-items: center;
  justify-content: space-between;
  flex-wrap: wrap;
  gap: 9px;
}

.room-heading h3 {
  margin: 0;
  font-family: Georgia, serif;
  font-size: 19px;
  font-weight: 500;
}

.room-capacity {
  padding: 5px 8px;
  color: var(--green);
  background: rgba(18, 95, 80, 0.08);
  border-radius: 2px;
  font-size: 10px;
  white-space: nowrap;
}

.dark-mode .room-capacity {
  background: rgba(161, 237, 222, 0.1);
}

.room-description {
  min-height: 60px;
  margin: 13px 0 16px;
  color: var(--muted);
  font-size: 11px;
  line-height: 1.8;
}

.room-separator {
  height: 1px;
  background: var(--border);
}

.room-features {
  display: grid;
  gap: 10px;
  min-height: 87px;
  margin: 17px 0;
  padding: 0;
  list-style: none;
}

.room-features li {
  display: flex;
  align-items: center;
  gap: 9px;
  font-size: 11px;
}

.check {
  color: var(--green);
  font-size: 13px;
  font-weight: 800;
}

.room-footer {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 10px;
  padding-top: 16px;
  border-top: 1px solid var(--border);
}

.price-container {
  display: grid;
  gap: 3px;
}

.price-label,
.price-period {
  color: var(--muted);
  font-size: 10px;
}

.price-container strong {
  color: var(--green);
  font-size: 21px;
}

.room-footer :deep(.button) {
  gap: 8px;
  padding: 11px 12px;
  font-size: 10px;
}

.catalog-disclaimer {
  margin-top: 23px;
  color: var(--muted);
  font-size: 10px;
  line-height: 1.7;
}

/* Invitación final */

.catalog-cta {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 25px;
  margin-bottom: 55px;
  padding: 38px max(7%, calc((100% - 1130px) / 2));
  background: rgba(18, 95, 80, 0.08);
}

.dark-mode .catalog-cta {
  background: rgba(161, 237, 222, 0.07);
}

.catalog-cta h2 {
  margin: 0;
  font-family: Georgia, serif;
  font-size: clamp(26px, 4vw, 35px);
  font-weight: 500;
}

.catalog-cta p {
  max-width: 460px;
  margin-bottom: 0;
  color: var(--muted);
  font-size: 12px;
  line-height: 1.8;
}

/* Adaptación para tabletas */

@media (max-width: 950px) {
  .hero-decoration {
    right: 3%;
    transform: translateY(-50%) scale(0.8);
  }

  .catalog-grid {
    grid-template-columns: repeat(2, minmax(0, 1fr));
  }
}

/* Adaptación para teléfonos */

@media (max-width: 650px) {
  .catalog-hero {
    min-height: 310px;
    padding: 45px 23px;
  }

  .catalog-hero h1 {
    font-size: 39px;
  }

  .hero-decoration {
    right: -120px;
    opacity: 0.35;
  }

  .catalog-intro {
    display: block;
    padding: 45px 22px 25px;
  }

  .catalog-intro > p {
    margin-top: 14px;
  }

  .catalog-container {
    padding: 0 20px 40px;
  }

  .catalog-grid {
    grid-template-columns: 1fr;
    gap: 20px;
  }

  .room-image {
    height: 220px;
  }

  .room-description {
    min-height: auto;
  }

  .room-features {
    min-height: auto;
  }

  .catalog-cta {
    align-items: flex-start;
    flex-direction: column;
    margin-bottom: 35px;
    padding: 32px 23px;
  }
}

@media (prefers-reduced-motion: reduce) {
  .catalog-page,
  .room-card,
  .room-image img {
    transition: none;
  }
}
</style>
```