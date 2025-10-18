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
  max-width: 900px;
  min-height: 100vh;
  margin: 20px auto 0 auto;
  padding: 0 14px;
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

/* -> Cambiado a grid para forzar botón a la derecha y evitar que la descripción lo empuje abajo */
.code-item {
  width: 100%;
  display: grid;
  grid-template-columns: 1fr auto; /* descripción flexible | botón auto */
  gap: 12px;
  align-items: start; /* alinear el botón al tope de la descripción multilinea */
  padding: clamp(4px, 1vw, 8px) clamp(12px, 3vw, 18px);
  border: 2px solid #6495ED;
  border-radius: 8px;
  background-color: #f8f9ff;
  transition: all 0.3s ease;
  font-family: monospace;
  outline: none;
  box-sizing: border-box;
}

/* Permitir que este grid-child se encoja correctamente */
.code-info {
  min-width: 0; /* MUY IMPORTANTE para evitar overflow en grid/flex */
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

/* botón: mantener compacto y evitar wrap interno */
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
  align-self: start; /* alinear al tope del grid cell */
}

/* Media query para móviles: conservar botón a la derecha en la mayoría de casos.
   En pantallas muy pequeñas (ej. <360px) apilamos para evitar overflow horizontal. */
@media (max-width: 600px) {
  .container-select {
    margin: 16px auto 0 auto;
    max-width: 100%;
    padding-left: 8px;
    padding-right: 8px;
    box-sizing: border-box;
    overflow-x: hidden; /* última línea de defensa */
    width: 95%;
  }

  .code-item {
    padding: clamp(6px, 1vw, 8px) clamp(10px, 3vw, 12px);
    grid-template-columns: 1fr auto;
    align-items: center;
    gap: 8px;
  }

  .select-button {
    padding: clamp(6px, 1vw, 10px) clamp(8px, 3vw, 12px);
    white-space: nowrap;
  }

  .code-description {
    font-size: clamp(12px, 3vw, 14px);
  }
}

/* Pantallas muy estrechas: apilar (botón abajo) para evitar scroll horizontal extremo */
@media (max-width: 360px) {
  .code-item {
    grid-template-columns: 1fr;
  }
  .select-button {
    width: auto;
    white-space: normal;
    margin-top: 8px;
    justify-self: start;
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
