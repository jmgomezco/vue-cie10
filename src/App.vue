<template>
  <ContainerInicial v-if="!mostrarSelect">
    <Marca textoMarca="NhugAi" class="marca-separada" />
    <CajaTexto @enviar="enviarTexto" />
    <Spinner v-if="loading" class="spinner-separado" />
  </ContainerInicial>
  <ContainerSelect
    v-else
    :sessionId="sessionId"
    :candidatos="candidatos"
    @reset="handleReset"
  />
</template>

<script setup>
import { ref } from 'vue'
import ContainerInicial from './components/ContainerInicial.vue'
import ContainerSelect from './components/ContainerSelect.vue'
import Marca from './components/Marca.vue'
import CajaTexto from './components/CajaTexto.vue'
import Spinner from './components/Spinner.vue'

const loading = ref(false)
const mostrarSelect = ref(false)
const sessionId = ref('')
const candidatos = ref([])

async function enviarTexto(texto) {
  if (texto && texto.trim() !== '') {
    loading.value = true
    mostrarSelect.value = false
    try {
      const res = await fetch('https://ffljaqyibd.execute-api.us-east-1.amazonaws.com/texto', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({ texto })
      })
      const data = await res.json()
      // Ajusta los nombres según la respuesta real de tu API
      sessionId.value = data.sessionId || ''
      candidatos.value = data.candidatos_gpt || []
      mostrarSelect.value = true
    } catch (e) {
      // Si hay error, muestra ContainerSelect sin resultados
      sessionId.value = ''
      candidatos.value = []
      mostrarSelect.value = true
    } finally {
      loading.value = false
    }
  }
}

function handleReset() {
  mostrarSelect.value = false
  sessionId.value = ''
  candidatos.value = []
}
</script>

<style scoped>
.marca-separada {
  margin-bottom: 60px;
}
.spinner-separado {
  margin-top: 5px;
  align-self: center;
}
</style>

<style>
html, body, #app {
  position: fixed;
  height: 100%;
  width: 100%;
  overflow: hidden !important;
  margin: 0;
  padding: 0;
}
</style>
