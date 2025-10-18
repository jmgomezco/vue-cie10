<template>
  <div class="app-container">
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
  </div>
</template>

<script setup>
import { ref } from 'vue'
import ContainerInicial from './components/ContainerInicial.vue'
import ContainerSelect from './components/ContainerSelect.vue'
import CajaTexto from './components/CajaTexto.vue'
import Spinner from './components/Spinner.vue'
import Marca from './components/Marca.vue' // <-- IMPORTADO para que el componente aparezca

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
/* Ajustes para que la Marca quede justificada a la izquierda en el primer contenedor */
.marca-separada {
  margin-top: 30px; /* aumenta el margen superior */
  margin-bottom: 45px;
  display: flex;
  justify-content: flex-start; /* contenido hacia la izquierda */
  align-items: center;
  align-self: flex-start; /* para que quede al inicio en contenedores flex */
  margin-left: 0;
  margin-right: auto; /* empuja hacia la izquierda cuando el padre es flex */
  text-align: left; /* por si el padre usa text-align:center */
}

.app-container {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  justify-content: flex-start;
  width: min(900px, 95%);
  max-width: 900px;
  margin: 20px auto 0; /* 20px desde el borde superior del viewport */
  padding: 0 14px;
  box-sizing: border-box;
  gap: 10px; /* 10px entre elementos hijos (verticalmente) */
  font-family: monospace;
}

  
  
/* Mantengo el estilo del spinner separado */
.spinner-separado {
  margin-top: 0px;
  align-self: center;
}
</style>
