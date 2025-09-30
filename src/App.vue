<template>
  <ContainerInicial>
    <Marca textoMarca="NhugAi" class="marca-separada" />
    <CajaTexto @enviar="enviarTexto" />
    <Spinner v-if="loading" class="spinner-separado" />
    <div v-if="respuesta" class="respuesta-api">
      <pre>{{ respuesta }}</pre>
    </div>
  </ContainerInicial>
</template>

<script setup>
import { ref } from 'vue'
import ContainerInicial from './components/ContainerInicial.vue'
import Marca from './components/Marca.vue'
import CajaTexto from './components/CajaTexto.vue'
import Spinner from './components/Spinner.vue'

const loading = ref(false)
const respuesta = ref('')

async function enviarTexto(texto) {
  if (texto && texto.trim() !== '') {
    loading.value = true
    respuesta.value = ''
    try {
      const res = await fetch('https://ffljaqyibd.execute-api.us-east-1.amazonaws.com/texto', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({ texto })
      })
      // Devuelve el JSON correcto de la API
      const data = await res.json()
      respuesta.value = JSON.stringify(data, null, 2)
    } catch (e) {
      respuesta.value = 'Error en la conexión con la API'
    } finally {
      loading.value = false
    }
  }
}
</script>

<style scoped>
.marca-separada {
  margin-bottom: 32px;
}
.spinner-separado {
  margin-top: 32px;
  align-self: center;
}
.respuesta-api {
  margin-top: 32px;
  font-size: 1.1em;
  color: #222;
  font-family: monospace;
  white-space: pre-wrap;
}
</style>
