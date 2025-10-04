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
        @click="elegirCodigo(item, idx)"
        @keydown.enter.prevent="elegirCodigo(item, idx)"
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
      <p>No se encontraros códigos para tu entrada.</p>
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
        Se grabó con exito tu selección
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
        // Cierra toda la app/página completamente
        window.close()
        // Si window.close() no funciona (no fue abierta por JS), puedes redirigir:
        // window.location.href = "about:blank";
      }, 2050)
    }
  } catch (e) {
    // Manejar errores si lo deseas
  } finally {
    loadingGrabado.value = false
  }
}

function reiniciar() {
  // No graba nada, solo resetea y vuelve al inicio
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
    width: 100%;
    max-width: 900px;
    min-height: 100vh;
    margin: 30px auto 0 auto;
    padding: 0 14px;
    box-sizing: border-box;
    font-family: monospace;
    position: relative;
    gap: 20px; /* separación vertical reducida a 20px */
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
.code-item {
    width: 100%;
    display: flex;
    justify-content: space-between;
    align-items: center;
    /* REDUCIDO ALTO VERTICAL: */
    padding: clamp(4px, 1vw, 8px) clamp(12px, 3vw, 18px);
    border: 2px solid #6495ED;
    border-radius: 8px;
    background-color: #f8f9ff;
    transition: all 0.3s ease;
    font-family: monospace;
    outline: none;
}
.code-item.activo {
    background-color: #bceeff !important;
    box-shadow: 0 2px 8px rgba(100, 149, 237, 0.2);
    transform: translateY(-1px);
}
.code-item:focus-visible,
.code-item:focus {
    background-color: #bceeff;
    outline: 2px solid #6495ED;
}
.code-item:hover {
    background-color: #bceeff;
}
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
}
.select-button:disabled {
    opacity: 0.6;
    cursor: not-allowed;
}
.code-info {
    flex: 1;
    display: flex;
    flex-direction: column;
    gap: 2px;
    font-family: monospace;
}
.code-number {
    font-family: monospace;
    color: #000;
}
.code-description {
    font-family: monospace;
    color: #000;
}
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
.new-search-button:hover,
.new-search-button:focus,
.new-search-button.active {
    background-color: #317f43 !important;
    color: #fff !important;
    transform: scale(1.05);
    font-family: monospace;
}
.no-codes-message p {
    font-family: monospace;
    color: #666;
    margin-bottom: clamp(10px, 2vh, 16px);
    font-size: clamp(12px, 2vw, 18px);
}
/* Popup confirmación */
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
@media (max-width: 600px) {
  .container-select {
    max-width: 100%;
    padding: 0 8px;
    margin: 24px auto 0 auto;
    gap: 20px;
  }
  .codes-list {
      gap: clamp(2px, 1vw, 6px);
      margin-top: clamp(2px, 1vw, 6px);
  }
  .codes-list,
  .code-item {
      font-size: clamp(12px, 3vw, 16px);
  }
  .code-item {
      padding: clamp(2px, 1vw, 6px) clamp(10px, 3vw, 14px);
  }
  .select-button {
      font-size: clamp(10px, 4vw, 12px);
      padding: clamp(6px, 1vw, 10px) clamp(10px, 3vw, 12px);
      color: #fff !important;
  }
  .action-buttons {
      margin-top: clamp(10px, 2vw, 16px);
  }
  .new-search-button {
      font-size: clamp(12px, 3vw, 14px);
      padding: clamp(7px, 1vw, 10px) clamp(12px, 3vw, 16px);
      color: #fff !important;
  }
  .new-search-button:hover,
  .new-search-button:focus,
  .new-search-button.active {
      color: #fff !important;
      background-color: #317f43 !important;
  }
  .no-codes-message {
      margin-top: clamp(8px, 2vw, 14px);
  }
  .no-codes-message p {
      font-size: clamp(12px, 3vw, 13px);
      margin-bottom: clamp(8px, 2vw, 12px);
  }
}
</style>
