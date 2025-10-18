<template>
  <div class="container-select">
    <Marca textoMarca="NhugAi" />
    <div class="codes-list" v-if="candidatos && candidatos.length">
      <div
        v-for="(item, idx) in candidatos"
        :key="item.codigo"
        class="code-item"
        :class="{ activo: isActive(idx) }"
        tabindex="0"
        @focus="setFocused(idx)"
        @blur="setFocused(null)"
        @mouseover="setHovered(idx)"
        @mouseleave="setHovered(null)"
      >
        <div class="code-info">
          <span class="code-number">{{ item.codigo }}</span>
          <span class="code-description">{{ item.desc }}</span>
        </div>
        <button
          class="select-button"
          type="button"
          :disabled="codigoGrabado || loadingGrabado"
          @click.stop="elegirCodigo(item, idx)"
        >
          Elegir
        </button>
      </div>
    </div>
    <div class="no-codes-message" v-else>
      <p>No se encontraron códigos para tu entrada.</p>
    </div>

    <!-- Botón SIEMPRE visible para volver a pantalla inicial -->
    <div class="action-buttons">
      <button class="new-search-button" @click="reiniciar">
        Prueba con otro texto
      </button>
    </div>

    <!-- Mensaje emergente de confirmación -->
    <transition name="fade">
      <div
        v-if="showConfirm"
        class="popup-confirm"
        role="status"
        aria-live="polite"
      >
        Se grabó con éxito tu selección
      </div>
    </transition>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import Marca from './Marca.vue'

const props = defineProps({
  sessionId: { type: String, required: true },
  candidatos: { type: Array, required: true }
})

const emit = defineEmits(['reset'])

const elegidoIdx = ref(null)
const focusedIdx = ref(null)
const hoveredIdx = ref(null)
const showConfirm = ref(false)
const loadingGrabado = ref(false)
const codigoGrabado = ref(false)

function isActive(idx) {
  return (
    (elegidoIdx.value === idx && codigoGrabado.value) ||
    focusedIdx.value === idx ||
    hoveredIdx.value === idx
  )
}

function setFocused(idx) {
  focusedIdx.value = idx
}
function setHovered(idx) {
  hoveredIdx.value = idx
}

async function elegirCodigo(item, idx) {
  if (codigoGrabado.value || loadingGrabado.value) return
  elegidoIdx.value = idx
  loadingGrabado.value = true
  try {
    const resp = await fetch('https://ffljaqyibd.execute-api.us-east-1.amazonaws.com/select', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        sessionId: props.sessionId,
        codigo: item.codigo,
        desc: item.desc
      })
    })
    if (resp.ok) {
      codigoGrabado.value = true
      showConfirm.value = true
      setTimeout(() => {
        showConfirm.value = false
        reiniciar()
      }, 2050)
    }
  } catch (e) {
    console.error(e)
  } finally {
    loadingGrabado.value = false
  }
}

function reiniciar() {
  elegidoIdx.value = null
  focusedIdx.value = null
  hoveredIdx.value = null
  codigoGrabado.value = false
  showConfirm.value = false
  emit('reset')
}
</script>

<style scoped>
/* prevenir sorpresas: box-sizing global dentro del scope */
*,
*::before,
*::after {
  box-sizing: border-box;
}

/* contenedor principal */
.container-select {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  justify-content: flex-start;
  width: 95%;
  /* ancho responsivo y suave para evitar cambios agresivos */
  max-width: clamp(720px, 88vw, 900px);
  min-height: 100vh;
  margin: 20px auto 0;
  padding: 0 12px; /* padding fijo, sin vw */
  font-family: monospace;
  position: relative;
  gap: 20px;
  overflow-x: visible;
}

/* lista */
.codes-list {
  width: 100%;
  display: flex;
  flex-direction: column;
  gap: clamp(6px, 1.2vw, 12px);
  margin-top: clamp(6px, 1vh, 12px);
}

/* base de fuente */
.codes-list,
.code-item {
  font-size: clamp(12px, 2vw, 18px);
}

/* ITEM: grid seguro */
.code-item {
  width: 100%;
  display: grid;
  grid-template-columns: minmax(0, 1fr) auto; /* clave: minmax(0,1fr) */
  gap: 10px;
  align-items: start;
  padding: 8px 10px; /* padding fijo y moderado */
  border: 2px solid #6495ED;
  border-radius: 8px;
  background-color: #f8f9ff;
  transition: all 0.18s ease;
  outline: none;
  box-sizing: border-box;
}

/* permitir que el bloque de texto se reduzca */
.code-info {
  min-width: 0; /* imprescindible */
  display: flex;
  flex-direction: column;
  gap: 4px;
}

/* descripción: permitir quiebre de palabras largas */
.code-description {
  color: #000;
  overflow-wrap: anywhere;
  word-break: break-word;
  hyphens: auto;
  white-space: normal;
}

/* botón: compacto, evitar que crezca demasiado */
.select-button {
  background-color: #6495ED;
  color: #fff !important;
  border: none;
  padding: 8px 10px; /* padding fijo */
  border-radius: 6px;
  font-family: monospace;
  font-weight: bold;
  cursor: pointer;
  transition: all 0.18s ease;
  white-space: nowrap;
  justify-self: end;
  align-self: start;
  max-width: 140px; /* proteger contra botones enormes */
  box-sizing: border-box;
}

/* imágenes y SVGs dentro de items (si hay) */
.code-item img,
.code-item svg {
  max-width: 100%;
  height: auto;
}

/* responsive: ajustes suaves para móvil */
@media (max-width: 600px) {
  .container-select {
    padding: 0 10px;
    width: 95%;
  }

  .code-item {
    gap: 8px;
    padding: 8px 8px;
  }

  .select-button {
    padding: 7px 10px;
    max-width: 120px;
  }
}

/* sólo en pantallas extremadamente pequeñas apilar (último recurso) */
@media (max-width: 360px) {
  .code-item {
    grid-template-columns: 1fr; /* apilar: descripción encima del botón */
  }
  .select-button {
    justify-self: start;
    margin-top: 8px;
    white-space: normal;
    max-width: 100%;
  }
}

/* resto de estilos visuales (popup y botones globales) */
.action-buttons {
  width: 100%;
  display: flex;
  justify-content: center;
  margin-top: clamp(12px, 3vh, 28px);
  font-family: monospace;
}
.new-search-button {
  background-color: #354e7b;
  color: #fff !important;
  border: none;
  padding: clamp(8px, 2vh, 16px) clamp(16px, 4vw, 24px);
  border-radius: 8px;
  font-family: monospace;
  font-size: clamp(15px, 3vw, 22px);
  font-weight: bold;
  cursor: pointer;
  transition: all 0.3s ease;
  text-decoration: none;
  display: inline-block;
}
.popup-confirm {
  position: fixed;
  top: 18px;
  left: 50%;
  transform: translateX(-50%);
  z-index: 10000;
  background: #317f43;
  color: #fff;
  padding: 20px 32px;
  border-radius: 12px;
  font-size: clamp(15px, 3vw, 22px);
  box-shadow: 0 4px 16px rgba(49, 127, 67, 0.16);
  font-family: monospace;
  font-weight: bold;
  text-align: center;
  opacity: 0.97;
  pointer-events: none;
}
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.35s;
}
.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
.fade-enter-to,
.fade-leave-from {
  opacity: 1;
}
</style>
