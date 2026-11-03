<script setup lang="ts">
import { computed, ref } from 'vue'

import refugiosierra from '@/imagenes/refugioenlasierra.webp'
import pac5 from '@/imagenes/paquete_5.jfif'
import pac1 from '@/imagenes/paquete_1.jfif'
import pac3 from '@/imagenes/paquete_3.jfif'
import pac2 from '@/imagenes/pac_2.jfif'
import pac4 from '@/imagenes/paquete_4.webp'
import pac6 from '@/imagenes/paquete_6.jpg'

import BaseButton from '@/components/BaseButton.vue'
import SiteHeader from '@/components/SiteHeader.vue'
import SiteFooter from '@/components/SiteFooter.vue'
import HabitacionesView from '@/views/HabitacionesView.vue'

const showBookingChoice = ref(false)
const bookingChoiceNotice = ref('')
const bookingFromSearch = ref(false);

const reservationMode = ref<'package' | 'lodging'>('package')

type View = 'home' | 'login' | 'register' | 'booking' | 'account' | 'habitaciones'

type PackageOption = {
  id: number
  name: string
  subtitle: string
  price: number
  duration: string
  image: string
  features: string[]
  capacity: number
  tag?: string
}

const packages: PackageOption[] = [
  {
    id: 1,
    name: 'Escapada Sierra',
    subtitle: 'Un respiro entre montañas',
    price: 3390,
    duration: '2 días · 1 noche',
    tag: 'Favorito',
    image: pac1,
    capacity: 4,
    features: [
      'Hospedaje para 4 personas',
      'Desayuno incluido',
      'Acceso a senderos'
    ]
  },
  {
    id: 2,
    name: 'Aventura Natural',
    subtitle: 'Explora, respira, descubre',
    price: 2890,
    duration: '3 días · 2 noches',
    image: pac2,
    capacity: 2,
    features: [
      'Hospedaje para 2 personas',
      'Experiencia guiada',
      'Desayuno y cena'
    ]
  },
  {
    id: 3,
    name: 'Refugio en la Sierra',
    subtitle: 'Desconecta sin prisa',
    price: 3490,
    duration: '3 días · 2 noches',
    image: pac3,
    capacity: 2,
    features: [
      'Habitación premium',
      'Desayuno incluido',
      'Experiencia para dos'
    ]
  },

    {
    id: 4,
    name: 'Aventura entre Montañas',
    subtitle: 'Una experiencia para compartir',
    price: 4990,
    duration: '3 días · 2 noches',
    image: pac4,
    capacity: 6,
    features: [
      'Cabaña para 6 personas',
      'Ruta de senderismo guiada',
      'Fogata nocturna con malvaviscos'
    ]
  },
  {
    id: 5,
    name: 'Escapada de Exploradores',
    subtitle: 'Descubre la magia del bosque',
    price: 4290,
    duration: '3 días · 2 noches',
    image: pac5,
    capacity: 5,
    features: [
      'Hospedaje para 5 personas',
      'Tour de exploración por el bosque',
      'Picnic con vistas a la montaña'
    ]
  },
  {
    id: 6,
    name: 'Noche bajo las Estrellas',
    subtitle: 'Conecta con la naturaleza',
    price: 2990,
    duration: '2 días · 1 noche',
    image: pac6,
    capacity: 3,
    features: [
      'Cabaña para 3 personas',
      'Observación de estrellas',
      'Chocolate caliente junto a la fogata'
    ]
  }
]

const activeView = ref<View>('home')
const selectedPackage = ref<PackageOption>(packages[0]!)
const darkMode = ref(false)
const mobileMenu = ref(false)

const guests = ref(2)
const checkIn = ref('')
const checkOut = ref('')

const today = (() => {
  const currentDate = new Date()
  const year = currentDate.getFullYear()
  const month = String(currentDate.getMonth() + 1).padStart(2, '0')
  const day = String(currentDate.getDate()).padStart(2, '0')

  return `${year}-${month}-${day}`
})()

// Datos independientes del buscador de estancias
const searchGuests = ref(2)
const searchCheckIn = ref('')
const searchCheckOut = ref('')

const fullName = ref('')
const email = ref('')
const password = ref('')

const notice = ref('')
const bookingStep = ref(1)

// Precios

const formattedPrice = (price: number) =>
  new Intl.NumberFormat('es-MX', {
    style: 'currency',
    currency: 'MXN',
    maximumFractionDigits: 0
  }).format(price)

// Cálculo de costo por noche

const nights = computed(() => {
  if (!checkIn.value || !checkOut.value) return 0

  const start = new Date(`${checkIn.value}T12:00:00`)
  const end = new Date(`${checkOut.value}T12:00:00`)

  return Math.max(
    0,
    Math.round((end.getTime() - start.getTime()) / 86400000)
  )
})

function goTo(view: View) {
  activeView.value = view
  mobileMenu.value = false
  notice.value = ''

  window.scrollTo({
    top: 0,
    behavior: 'smooth'
  })
}

function goToSection(section: string) {
  activeView.value = 'home'
  mobileMenu.value = false
  notice.value = ''

  window.setTimeout(() => {
    document.getElementById(section)?.scrollIntoView({
      behavior: 'smooth',
      block: 'start'
    })
  }, 50)
}

function book(option: PackageOption) {
  if (!bookingFromSearch.value) {
    checkIn.value = '';
    checkOut.value = '';
  }

  bookingFromSearch.value = false;

  reservationMode.value = 'package';
  selectedPackage.value = option;
  bookingStep.value = 1;
  notice.value = '';

  showBookingChoice.value = false;
  activeView.value = 'booking';
}

function searchStay() {
  bookingFromSearch.value = true;

  guests.value = searchGuests.value;
  checkIn.value = searchCheckIn.value;
  checkOut.value = searchCheckOut.value;

  searchGuests.value = 2;
  searchCheckIn.value = '';
  searchCheckOut.value = '';
-
  openBookingChoice();
}

// Abrir ventana para elegir reservación
function openBookingChoice() {
  bookingChoiceNotice.value = guests.value > 6
    ? 'Todavía no tenemos capacidad confirmada para '
      + guests.value
      + ' huéspedes. Puedes enviar una solicitud de hospedaje '
      + 'para que el hotel confirme la disponibilidad.'
    : ''

  showBookingChoice.value = true
}

// Reservar solamente hospedaje
function bookLodging() {
  reservationMode.value = 'lodging'
  bookingStep.value = 1
  notice.value = ''
  showBookingChoice.value = false
  bookingChoiceNotice.value = ''

  // No borrar las fechas seleccionadas
  goTo('booking')
}

function chooseSuitablePackage() {
  const suitablePackages = packages
    .filter(option => option.capacity >= guests.value)
    .sort((a, b) => a.capacity - b.capacity)

  if (suitablePackages.length === 0) {
    bookingChoiceNotice.value =
      `No tenemos un paquete con capacidad confirmada para ${
        guests.value
      } huéspedes. Puedes solicitar hospedaje para consultar disponibilidad.`

    return
  }

  book(suitablePackages[0]!)
}

// En esta parte se debe conectar el backend//
function submitAccount() {
  if (activeView.value === 'register' && !fullName.value.trim()) {
    notice.value = 'Escribe tu nombre para continuar.'
    return
  }

  if (!email.value.includes('@') || password.value.length < 6) {
    notice.value =
      'Revisa tu correo y utiliza una contraseña de al menos 6 caracteres.'
    return
  }

  notice.value =
    'Esta pantalla es una demostración visual.'
}

function nextBookingStep() {
  if (
    bookingStep.value === 1 &&
    (!checkIn.value || !checkOut.value || nights.value < 1)
  ) {
    notice.value =
      'Selecciona una fecha de salida posterior a la llegada.'
    return
  }
// Aqui va una API para pago //
  notice.value = ''

  if (bookingStep.value < 3) {
    bookingStep.value++
  } else {
    notice.value =
      '¡Tu selección está lista!.'
  }
}
</script>

<template>
  <div class="site-shell" :class="{ 'dark-mode': darkMode }">

    <!-- Componente de encabezado -->

    <SiteHeader
      v-if="activeView !== 'habitaciones'"
      :dark-mode="darkMode"
      @toggle-theme="darkMode = !darkMode"
      @navigate="goTo"
      @section="goToSection"
    />

    <main v-if="activeView === 'home'">

      <!-- Banner -->

      <section class="hero">
        <div class="hero-shade"></div>

        <div class="hero-content">
          <p class="eyebrow light-eyebrow">
            <span></span> TU REFUGIO ENTRE MONTAÑAS
          </p>

          <h1>
            Donde la naturaleza<br />
            <em>te encuentra.</em>
          </h1>

          <p class="hero-description">
            Porque las historias comienzan al pie de la montaña.
          </p>

          <div class="hero-actions">
            <BaseButton
              variant="coral"
              @click="goToSection('experiencias')"
            >
              Explorar experiencias <span>↗</span>
            </BaseButton>

            <a class="text-link light-link" href="#historia">
              Conoce nuestro refugio <span>↓</span>
            </a>
          </div>

          <div class="hero-note">
            <span class="note-line"></span>
            <span>UN LUGAR PARA VOLVER A TI</span>
          </div>
        </div>

        <div class="hero-index">
          <span>01</span>
          <i></i>
          <span>03</span>
        </div>
      </section>

      <!-- BUSCADOR DE ESTANCIAS -->

      <section class="booking-strip" aria-label="Buscar estancia">
        <div class="booking-intro">
          <span class="booking-icon">⌁</span>

          <div>
            <strong>Planea tu escapada</strong>
            <small>Tu próxima historia empieza aquí</small>
          </div>
        </div>

       <!-- Fecha de entrada -->
<label>
  Entrada

  <input
    v-model="searchCheckIn"
    type="date"
    :min="today"
    aria-label="Fecha de entrada"
  />
</label>

<!-- Fecha de salida -->
<label>
  Salida

  <input
    v-model="searchCheckOut"
    type="date"
    :min="searchCheckIn || today"
    aria-label="Fecha de salida"
  />
</label>

<!-- Huéspedes -->
<label>
  Huéspedes

  <select v-model.number="searchGuests">
    <option :value="1">1 persona</option>
    <option :value="2">2 personas</option>
    <option :value="3">3 personas</option>
    <option :value="4">4 personas</option>
    <option :value="5">5 personas</option>
    <option :value="6">6 personas</option>
  </select>
</label>

<BaseButton
  class="search-button"
  variant="green"
  @click="searchStay"
>
  Buscar estancia <span>→</span>
</BaseButton>

<div
  v-if="showBookingChoice"
  class="booking-choice-overlay"
  @click.self="showBookingChoice = false"
>
  <section
    class="booking-choice-modal"
    role="dialog"
    aria-modal="true"
    aria-labelledby="booking-choice-title"
  >
    <button
      type="button"
      class="booking-choice-close"
      aria-label="Cerrar ventana"
      @click="showBookingChoice = false"
    >
      ×
    </button>

    <p class="eyebrow">
      <span></span> PREPARA TU ESCAPADA
    </p>

    <h2 id="booking-choice-title">
      ¿Cómo deseas reservar?
    </h2>

    <p class="booking-choice-description">
      Has seleccionado {{ guests }}
      {{ guests === 1 ? 'huésped' : 'huéspedes' }}.
      Elige la opción que mejor se adapte a tu viaje.
    </p>

    <p
      v-if="bookingChoiceNotice"
      class="notice"
      role="status"
    >
      {{ bookingChoiceNotice }}
    </p>

    <button
      type="button"
      class="booking-choice-option"
      @click="chooseSuitablePackage"
    >
      <span class="booking-choice-icon">✧</span>

      <span class="booking-choice-copy">
        <strong>Contratar un paquete</strong>
        <small>
          Descubre experiencias con servicios incluidos
          y capacidad adecuada para tus huéspedes.
        </small>
      </span>

      <span class="booking-choice-arrow">→</span>
    </button>

    <button
      type="button"
      class="booking-choice-option"
      @click="bookLodging"
    >
      <span class="booking-choice-icon">⌂</span>

      <span class="booking-choice-copy">
        <strong>Solo reservar hospedaje</strong>
        <small>
          Continúa sin contratar un paquete de experiencias.
          La disponibilidad deberá confirmarse con el hotel.
        </small>
      </span>

      <span class="booking-choice-arrow">→</span>
    </button>

    <button
      type="button"
      class="booking-choice-cancel"
      @click="showBookingChoice = false"
    >
      Seguir explorando
    </button>
  </section>
</div>
      </section>

      <section id="historia" class="intro-section section-wrap">
        <div class="intro-image">
          <img
            :src="refugiosierra"
            alt="Refugio rodeado de naturaleza"
          />

          <span class="image-stamp">
            SIERRA<br />
            DE SANTA
          </span>
        </div>

        <div class="intro-copy">
          <p class="eyebrow">
            <span></span> MÁS QUE UNA ESTANCIA
          </p>

          <h2>
            El lujo de<br />
            <em>desconectarte.</em>
          </h2>

          <p>
            Hay lugares que te invitan a bajar el ritmo, respirar profundo y
            recordar lo esencial. En Sierra de Santa, cada amanecer es una
            nueva oportunidad para conectar con la naturaleza y contigo.
          </p>

          <a class="text-link" href="#experiencias">
            Descubre nuestra esencia <span>↗</span>
          </a>

          <div class="intro-stat">
            <strong>100%</strong>
            <span>
              momentos<br />
              para recordar
            </span>
          </div>
        </div>
      </section>

      <section id="experiencias" class="packages-section">
        <div class="section-wrap">
          <div class="section-heading">
            <div>
              <p class="eyebrow">
                <span></span> ELIGE TU AVENTURA
              </p>

              <h2>
                Experiencias <em>que conectan.</em>
              </h2>
            </div>

            <p>
              Desde una escapada tranquila hasta una aventura en la sierra.
              Encuentra la experiencia que va contigo.
            </p>
          </div>

          <div class="package-grid">
            <article
              v-for="item in packages"
              :key="item.id"
              class="package-card"
            >
              <div class="package-image">
                <img
                  :src="item.image"
                  :alt="item.name"
                  loading="lazy"
                />

                <span v-if="item.tag" class="package-tag">
                  {{ item.tag }}
                </span>

                <span class="package-duration">
                  {{ item.duration }}
                </span>
              </div>

              <div class="package-body">
                <p class="package-kicker">
                  PAQUETE {{ String(item.id).padStart(2, '0') }}
                </p>

                <h3>{{ item.name }}</h3>

                <p class="package-subtitle">
                  {{ item.subtitle }}
                </p>

                <ul>
                  <li
                    v-for="feature in item.features"
                    :key="feature"
                  >
                    ✓ {{ feature }}
                  </li>
                </ul>

                <div class="package-footer">
                  <div>
                    <small>Desde</small>

                    <strong>
                      {{ formattedPrice(item.price) }}
                      <small>/ paquete</small>
                    </strong>
                  </div>

                  <BaseButton
                    variant="outline"
                    @click="book(item)"
                  >
                    Elegir <span>↗</span>
                  </BaseButton>
                </div>
              </div>
            </article>
          </div>
        </div>
      </section>

      <!-- Banner de abajo -->

      <section id="habitaciones" class="rooms-band">
        <div class="rooms-photo"></div>

        <div class="rooms-copy">
          <p class="eyebrow light-eyebrow">
            <span></span> DESCANSO CON PROPÓSITO
          </p>


          <h2>
            Tu refugio.<br />
            <em>Tu ritmo.</em>
          </h2>

          <p>
            Espacios pensados para descansar, despertar con calma y disfrutar
            el paisaje sin prisas.
          </p>

        </div>
      </section>

<section id="opiniones" class="review-section section-wrap">
  <p class="eyebrow">
    <span></span> QUIENES YA NOS VISITARON
  </p>

  <h2>
    Historias que <em>se quedan.</em>
  </h2>

  <div class="reviews-grid">
    <div class="review-card">
      <div class="stars">★★★★★</div>

      <blockquote>
        “Un lugar para respirar, desconectarnos del ruido y disfrutar de
        verdad. Nos llevamos recuerdos preciosos y muchas ganas de volver.”
      </blockquote>

      <div class="review-person">
        <span class="avatar">M</span>

        <div>
          <strong>Mariana R.</strong>
          <small>Una escapada en pareja</small>
        </div>

        <span class="review-verified">✓ ESTANCIA VERIFICADA</span>
      </div>
    </div>

    <div class="review-card">
      <div class="stars">★★★★★</div>

      <blockquote>
        “La tranquilidad del lugar fue justo lo que necesitábamos.
        Disfrutamos el paisaje, el desayuno y cada momento de nuestra estancia.”
      </blockquote>

      <div class="review-person">
        <span class="avatar">A</span>

        <div>
          <strong>Andrea L.</strong>
          <small>Fin de semana de descanso</small>
        </div>

        <span class="review-verified">✓ ESTANCIA VERIFICADA</span>
      </div>
    </div>

    <div class="review-card">
      <div class="stars">★★★★★</div>

      <blockquote>
        “Nos encantó estar rodeados de naturaleza. La experiencia fue
        especial y pudimos olvidarnos por un rato de la rutina diaria.”
      </blockquote>

      <div class="review-person">
        <span class="avatar">D</span>

        <div>
          <strong>Daniel M.</strong>
          <small>Aventura en pareja</small>
        </div>

        <span class="review-verified">✓ ESTANCIA VERIFICADA</span>
      </div>
    </div>

    <!-- Opinión 4 -->
    <div class="review-card">
      <div class="stars">★★★★★</div>

      <blockquote>
        “Un refugio ideal para descansar, disfrutar del aire fresco y
        compartir tiempo de calidad. Nos gustaría regresar pronto.”
      </blockquote>

      <div class="review-person">
        <span class="avatar">S</span>

        <div>
          <strong>Sofía G.</strong>
          <small>Escapada de relajación</small>
        </div>

        <span class="review-verified">✓ ESTANCIA VERIFICADA</span>
      </div>
    </div>
  </div>
</section>

      <section class="closing-cta">
        <p class="eyebrow light-eyebrow">
          <span></span> TU PRÓXIMA HISTORIA TE ESPERA
        </p>

        <h2>
          La montaña te está<br />
          <em>llamando.</em>
        </h2>
        <p class="eyebrow light-eyebrow">
          <span></span> TE ESPERAMOS.
        </p>
      </section>
    </main>

    <!-- Catálogo de habitaciones -->
      <HabitacionesView
        v-else-if="activeView === 'habitaciones'"
        :dark-mode="darkMode"
        @toggle-theme="darkMode = !darkMode"
        @navigate="goTo"
        @section="goToSection"
      />

    <!-- Sesion y registro -->

    <main
      v-else-if="activeView === 'login' || activeView === 'register'"
      class="auth-page"
    >
      <div class="auth-visual">
        <div>
          <p class="eyebrow light-eyebrow">
            <span></span> HOTEL SIERRA DE SANTA
          </p>

          <h1>
            {{
              activeView === 'login'
                ? 'Qué bueno tenerte de vuelta.'
                : 'Tu próxima historia comienza aquí.'
            }}
          </h1>

          <p>
            Porque las historias comienzan al pie de la montaña.
          </p>
        </div>
      </div>

      <form class="auth-card" @submit.prevent="submitAccount">

        <p class="eyebrow">
          {{
            activeView === 'login'
              ? 'BIENVENIDO DE NUEVO'
              : 'ÚNETE A NUESTRA COMUNIDAD'
          }}
        </p>

        <h2>
          {{
            activeView === 'login'
              ? 'Iniciar sesión'
              : 'Crear una cuenta'
          }}
        </h2>

        <p class="muted">
          {{
            activeView === 'login'
              ? 'Ingresa tus datos para continuar.'
              : 'Guarda tus reservaciones y prepara tu próxima escapada.'
          }}
        </p>

        <label v-if="activeView === 'register'">
          Nombre completo

          <input
            v-model="fullName"
            autocomplete="name"
            placeholder="Tu nombre"
            required
          />
        </label>

        <label>
          Correo electrónico

          <input
            v-model="email"
            type="email"
            autocomplete="email"
            placeholder="tu@correo.com"
            required
          />
        </label>

        <label>
          Contraseña

          <input
            v-model="password"
            type="password"
            :autocomplete="
              activeView === 'login'
                ? 'current-password'
                : 'new-password'
            "
            placeholder="Mínimo 6 caracteres"
            minlength="6"
            required
          />
        </label>

        <p v-if="notice" class="notice">
          {{ notice }}
        </p>

        <BaseButton
          class="full-button"
          variant="green"
          type="submit"
        >
          {{
            activeView === 'login'
              ? 'Entrar a mi cuenta'
              : 'Crear cuenta'
          }}
          <span>→</span>
        </BaseButton>

        <p class="auth-switch">
          {{
            activeView === 'login'
              ? '¿Aún no tienes cuenta?'
              : '¿Ya tienes cuenta?'
          }}

          <button
            type="button"
            @click="
              goTo(activeView === 'login' ? 'register' : 'login')
            "
          >
            {{
              activeView === 'login'
                ? 'Regístrate'
                : 'Inicia sesión'
            }}
          </button>
        </p>
      </form>
    </main>

    <main
      v-else-if="activeView === 'booking'"
      class="booking-page section-wrap"
    >

      <div class="booking-page-heading">

        <h1>
          Tu estancia, <em>a tu manera.</em>
        </h1>

        <p>
          Completa los pasos para preparar tu reservación.
        </p>
      </div>

      <div class="steps">
        <div
          v-for="step in 3"
          :key="step"
          class="step"
          :class="{ active: bookingStep >= step }"
        >
          <span>{{ step }}</span>

          {{
            [
              'Fechas y huéspedes',
              'Revisa tu selección',
              'Confirmación'
            ][step - 1]
          }}
        </div>
      </div>

      <div
  class="booking-layout"
  :class="{ 'lodging-only': reservationMode === 'lodging' }"
>
        <form
          class="booking-form"
          @submit.prevent="nextBookingStep"
        >
          <template v-if="bookingStep === 1">
            <h2>¿Cuándo nos visitas?</h2>

            <p class="muted">
              Elige tus fechas y cuántas personas te acompañan.
            </p>

            <div class="field-grid">
              <label>
                Fecha de llegada

                <input
                  v-model="checkIn"
                  type="date"
                  required
                />
              </label>

              <label>
                Fecha de salida

                <input
                  v-model="checkOut"
                  type="date"
                  :min="checkIn || undefined"
                  required
                />
              </label>
            </div>

            <label>
              Huéspedes

              <select v-model.number="guests">
                <option v-for="n in 6" :key="n" :value="n">
                  {{ n }} {{ n === 1 ? 'persona' : 'personas' }}
                </option>
              </select>
            </label>
          </template>

          <template v-else-if="bookingStep === 2">
            <h2>Revisa tu próxima experiencia</h2>

            <p class="muted">
              Asegúrate de que todo esté como lo deseas.
            </p>

            <div class="summary-line">
              <span>Tipo de reservación</span>
              <strong>
                {{
                  reservationMode === 'package'
                    ? 'Paquete de experiencia'
                    : 'Solo hospedaje'
                }}
              </strong>
            </div>

            <div
              v-if="reservationMode === 'package'"
              class="summary-line"
            >
              <span>Experiencia</span>
              <strong>{{ selectedPackage.name }}</strong>
            </div>

            <div class="summary-line">
              <span>Fechas</span>
              <strong>{{ checkIn }} — {{ checkOut }}</strong>
            </div>

            <div class="summary-line">
              <span>Duración</span>
              <strong>{{ nights }} noches</strong>
            </div>

            <div class="summary-line">
              <span>Huéspedes</span>
              <strong>{{ guests }}</strong>
            </div>
          </template>

          <template v-else-if="bookingStep === 3">
            <h2>¡Ya casi está!</h2>

            <p class="muted">
              Tu selección está preparada. Para completar una reservación
              real, el sistema deberá validar disponibilidad y procesar el
              pago de forma segura.
            </p>

            <div class="demo-message">
              No se ha realizado ningún cargo ni reservación.
              Es solo una prueba.
            </div>
          </template>

          <p v-if="notice" class="notice">
            {{ notice }}
          </p>

          <div class="form-actions">
            <BaseButton
              v-if="bookingStep > 1"
              variant="outline"
              @click="bookingStep--; notice = ''"
            >
              Anterior
            </BaseButton>

            <BaseButton
              variant="green"
              type="submit"
            >
              {{
                bookingStep < 3
                  ? 'Continuar'
                  : 'Finalizar demostración'
              }}
              <span>→</span>
            </BaseButton>
          </div>
        </form>

        <!-- Resumen de reservación: solamente aparece cuando hay un paquete -->
<aside
  v-if="reservationMode === 'package'"
  class="booking-summary"
>
  <img
    :src="selectedPackage.image"
    :alt="selectedPackage.name"
  />

  <div class="booking-summary-body">
    <p class="package-kicker">TU EXPERIENCIA</p>

    <h3>{{ selectedPackage.name }}</h3>

    <p class="muted">
      {{ selectedPackage.subtitle }}
    </p>

    <ul>
      <li
        v-for="feature in selectedPackage.features"
        :key="feature"
      >
        ✓ {{ feature }}
      </li>
    </ul>

  </div>
</aside>
      </div>
    </main>

    <!-- Ocupo el backend para esta parte -->

    <main v-else class="account-page section-wrap">
      <button
        type="button"
        class="back-link"
        @click="goTo('home')"
      >
        ← Volver al inicio
      </button>

      <p class="eyebrow">
        <span></span> TU ESPACIO PERSONAL
      </p>

      <h1>Tu próxima historia te espera.</h1>

      <p>
        Falta Backend.
      </p>

      <BaseButton
        variant="green"
        @click="goTo('login')"
      >
        Iniciar sesión
      </BaseButton>
    </main>

    <SiteFooter
      v-if="activeView !== 'habitaciones'"
      @navigate="goTo"
      @section="goToSection"
    />

  </div>
</template>
