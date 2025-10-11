<template>
  <ContainerInicial v-if="!mostrarSelect">
    <Sobre class="marca-separada" />
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
import Sobre from './components/Sobre.vue'
import CajaTexto from './components/CajaTexto.vue'
import Spinner from './components/Spinner.vue'

function getCookie(name) {
  const value = `; ${document.cookie}`;
  const parts = value.split(`; ${name}=`);
  if (parts.length === 2) return parts.pop().split(';').shift();
  return null;
}

const loading = ref(false)
const mostrarSelect = ref(false)
const sessionId = ref('')
const candidatos = ref([])

// --- Modifica la función para enviar el token en el header
async function enviarTexto(texto) {
  if (texto && texto.trim() !== '') {
    loading.value = true
    mostrarSelect.value = false

    // Leer el token de la cookie
    const cognitoToken = getCookie('cognito_token')

    try {
      const res = await fetch('https://ffljaqyibd.execute-api.us-east-1.amazonaws.com/texto', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          ...(cognitoToken ? { 'Authorization': `Bearer ${cognitoToken}` } : {})
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
  margin-top: 45px; /* aumenta el margen superior */
  margin-bottom: 45px;
  display: flex;
  justify-content: center;
  align-items: center;
}
.spinner-separado {
  margin-top: 0px;
  align-self: center;
}
</style>
