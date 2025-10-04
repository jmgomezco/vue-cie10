<template>
  <div class="container-select">
    <Marca textoMarca="NhugAi" />
    <div class="codes-list" v-if="candidatos && candidatos.length">
      <div
        v-for="(item, idx) in candidatos"
        :key="item.codigo"
        class="code-item"
        :class="{ activo: elegidoIdx === idx }"
      >
        <div class="code-info">
          <span class="code-number">{{ item.codigo }}</span>
          <span class="code-description">{{ item.desc }}</span>
        </div>
        <button
          class="select-button"
          :disabled="elegidoIdx !== null"
          @click="elegirCodigo(item, idx)"
        >
          Elegir
        </button>
      </div>
    </div>
    <div class="no-codes-message" v-else>
      <p>No hay códigos disponibles</p>
    </div>
    <div class="action-buttons" v-if="elegidoIdx !== null">
      <button class="new-search-button" @click="reiniciar">Nueva búsqueda</button>
    </div>
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

async function elegirCodigo(item, idx) {
  elegidoIdx.value = idx
  try {
    await fetch('https://ffljaqyibd.execute-api.us-east-1.amazonaws.com/select', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        sessionId: props.sessionId,
        codigo: item.codigo,
        desc: item.desc
      })
    })
    // Puedes mostrar feedback de éxito aquí si lo deseas
  } catch (e) {
    // Puedes mostrar feedback de error aquí si lo deseas
  }
}

function reiniciar() {
  elegidoIdx.value = null
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
    margin: 60px auto 0 auto;
    padding: 0 14px;
    box-sizing: border-box;
    font-family: monospace;
    position: relative;
    gap: 32px;
}

/* --- Pantalla de resultados - fuentes más grandes (excepto h2 y botón nueva búsqueda) --- */
.codes-list {
    width: 100%;
    display: flex;
    flex-direction: column;
    gap: clamp(3px, 1vw, 10px);
    margin-top: clamp(4px, 1vh, 12px);
    font-family: monospace;
}

.code-item {
    width: 100%;
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: clamp(6px, 1vw, 12px);
    border: 2px solid #6495ED;
    border-radius: 8px;
    background-color: #f8f9ff;
    transition: all 0.3s ease;
    font-family: monospace;
}

.code-item.activo {
    background-color: #bceeff;
    box-shadow: 0 2px 8px rgba(100, 149, 237, 0.2);
    transform: translateY(-1px);
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
}

@media (max-width: 600px) {
  .container-select {
    max-width: 100%;
    padding: 0 8px;
    margin: 24px auto 0 auto;
  }
  .codes-list {
      gap: clamp(2px, 1vw, 6px);
      margin-top: clamp(2px, 1vw, 6px);
  }
  .code-item {
      padding: clamp(4px, 1vw, 8px);
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
