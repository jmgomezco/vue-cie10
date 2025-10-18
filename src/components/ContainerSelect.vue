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
.container-select {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  justify-content: flex-start;
  width: 95%;
  /* ancho responsivo y suave para evitar cambios agresivos */
  max-width: clamp(720px, 88vw, 900px);
  min-height: 100vh;
  margin: 20px auto 0 auto;
  padding: 0 12px;
  box-sizing: border-box;
  font-family: monospace;
  position: relative;
  gap: 20px;
}

.codes-list {
  width: 100%;
  display: flex;
  flex-direction: column;
  gap: clamp(3px, 1vw, 10px);
  margin-top: clamp(4px, 1vh, 12px);
  font-family: monospace;
}

.codes-list,
.code-item {
  font-size: clamp(12px, 2vw, 18px);
}

/* Usar grid con minmax para evitar que la descripción empuje el botón */
.code-item {
  width: 100%;
  display: grid;
  grid-template-columns: minmax(0, 1fr) auto; /* descripción flexible | botón fijo */
  gap: 12px;
  align-items: start; /* botón alineado al tope de la descripción multilinea */
  padding: clamp(4px, 1vw, 8px) clamp(12px, 3vw, 18px);
  border: 2px solid #6495ED;
  border-radius: 8px;
  background-color: #f8f9ff;
  transition: all 0.3s ease;
  font-family: monospace;
  outline: none;
  box-sizing: border-box;
}

/* Permitir que el bloque de texto se encoja dentro de la celda del grid */
.code-info {
  min-width: 0; /* clave para evitar overflow que empuja al botón */
  display: flex;
  flex-direction: column;
  gap: 2px;
  font-family: monospace;
}

/* permitir quiebres de palabras largas en la descripción */
.code-description {
  font-family: monospace;
  color: #000;
  overflow-wrap: anywhere;
  word-break: break-word;
  white-space: normal;
}

/* botón: compacto y pegado a la columna derecha */
.select-button {
  background-color: #6495ED;
  color: #fff !important;
  border: none;
  padding: clamp(7px, 1vw, 14px) clamp(12px, 3vw, 18px);
  border-radius: 6px;
  font-family: monospace;
  font-weight: bold;
  cursor: pointer;
  transition: all 0.3s ease;
  white-space: nowrap;
  justify-self: end; /* asegurar que quede a la derecha */
  align-self: start;
  box-sizing: border-box;
}

/* Media query para móviles (suave): conservar botón a la derecha salvo pantallas muy pequeñas */
@media (max-width: 600px) {
  .container-select {
    margin: 16px auto 0 auto;
    max-width: 100%;
    padding-left: 8px;
    padding-right: 8px;
    box-sizing: border-box;
    overflow-x: hidden;
    width: 95%;
  }

  .code-item {
    padding: clamp(6px, 1vw, 8px) clamp(10px, 3vw, 12px);
    grid-template-columns: minmax(0, 1fr) auto;
    gap: 8px;
    align-items: center;
  }

  .select-button {
    padding: clamp(6px, 1vw, 10px) clamp(8px, 3vw, 12px);
    white-space: nowrap;
  }

  .code-description {
    font-size: clamp(12px, 3vw, 14px);
  }
}

/* Solo en pantallas extremadamente estrechas apilamos (botón abajo) para evitar scroll horizontal */
@media (max-width: 360px) {
  .code-item {
    grid-template-columns: 1fr;
  }
  .select-button {
    justify-self: start;
    margin-top: 8px;
    white-space: normal;
  }
}

/* resto de estilos (sin cambios) */
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
