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
/* safety: component-scoped box-sizing for predictable sizing */
*,
*::before,
*::after {
  box-sizing: border-box;
}

/* Container: scale max-width proportionally with viewport, but never overflow */
.container-select {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  justify-content: flex-start;

  /* make the container grow proportionally with screen but constrained to viewport */
  width: 100%;
  max-width: clamp(780px, 92vw, 1200px); /* increases on larger screens, shrinks on small screens */
  margin: 20px auto 0;
  padding: 0 18px; /* moderate padding that doesn't use vw, avoids adding to overflow */
  min-height: 100vh;
  box-sizing: border-box;
  font-family: monospace;
  position: relative;
  gap: 20px;

  /* ensure no horizontal scroll caused by children escaping the box */
  overflow-x: hidden;
}

/* list wrapper */
.codes-list {
  width: 100%;
  display: flex;
  flex-direction: column;
  gap: clamp(6px, 1.2vw, 14px);
  margin-top: clamp(6px, 1vh, 12px);
}

/* base font sizing */
.codes-list,
.code-item {
  font-size: clamp(12px, 2vw, 18px);
}

/* code item: use grid with flexible column and fixed button column,
   and make inner elements able to shrink to avoid overflow. */
.code-item {
  width: 100%;
  display: grid;
  grid-template-columns: minmax(0, 1fr) auto; /* flexible desc | button */
  gap: 12px;
  align-items: start;
  padding: clamp(8px, 1vw, 14px);
  border: 2px solid #6495ED;
  border-radius: 8px;
  background-color: #f8f9ff;
  transition: all 0.18s ease;
  outline: none;
  box-sizing: border-box;
}

/* allow the left column to shrink */
.code-info {
  min-width: 0; /* critical to allow shrink in flex/grid */
  display: flex;
  flex-direction: column;
  gap: 6px;
  word-break: break-word;
}

/* description: wrap and prevent extremely long words from overflowing */
.code-description {
  color: #000;
  overflow-wrap: anywhere;
  word-break: break-word;
  hyphens: auto;
  white-space: normal;
}

/* button: keep compact with max-width and allow shrink on smaller screens */
.select-button {
  background-color: #6495ED;
  color: #fff !important;
  border: none;
  padding: clamp(8px, 1vw, 12px) clamp(10px, 2.2vw, 16px);
  border-radius: 6px;
  font-family: monospace;
  font-weight: bold;
  font-size: clamp(14px, 1.6vw, 16px);
  cursor: pointer;
  transition: all 0.18s ease;
  flex: 0 0 auto;
  white-space: nowrap;
  max-width: 160px;
  box-sizing: border-box;
}

/* Ensure images/svgs don't overflow */
.code-item img,
.code-item svg {
  max-width: 100%;
  height: auto;
}

/* Responsive adjustments: increase container max-width slightly for tablets/desktop,
   and ensure inner elements shrink to avoid overflow in narrow screens. */
@media (min-width: 1200px) {
  /* for very large screens let the container be a bit wider */
  .container-select {
    max-width: 1400px;
    padding: 0 24px;
  }
}

/* Mobile: reduce paddings, button max-width and font so everything fits horizontally */
@media (max-width: 600px) {
  .container-select {
    padding: 0 12px;
    max-width: 98vw; /* keep under viewport */
  }

  .code-item {
    gap: 8px;
    padding: 8px 10px;
    grid-template-columns: minmax(0, 1fr) auto; /* keep button on the right */
  }

  .select-button {
    padding: 8px 10px;
    max-width: 110px;
    font-size: 15px;
  }

  /* If a description is extremely long, allow it to wrap freely but reduce risk of overflow */
  .code-description {
    font-size: 14px;
    line-height: 1.3;
  }
}

/* Very narrow screens: as absolute last resort stack button below to avoid any overflow,
   but with current sizing this should rarely be hit. */
@media (max-width: 340px) {
  .code-item {
    grid-template-columns: 1fr; /* stack */
  }
  .select-button {
    justify-self: start;
    margin-top: 8px;
    white-space: normal;
    max-width: 100%;
  }
}

/* action buttons and popup remain unchanged */
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
