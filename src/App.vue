<template>
  <ContainerInicial v-if="!mostrarSelect">
    <Marca textoMarca="NhugAi" class="marca-separada" />
    <CajaTexto @enviar="enviarTexto" />
    <Spinner v-if="loading" class="spinner-separado" />
  </ContainerInicial>
  <ContainerSelect v-else />
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

async function enviarTexto(texto) {
  if (texto && texto.trim() !== '') {
    loading.value = true
    // Oculta ContainerSelect mientras carga
    mostrarSelect.value = false
    try {
      await fetch('https://ffljaqyibd.execute-api.us-east-1.amazonaws.com/texto', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({ texto })
      })
    } catch (e) {
      // puedes loguear si quieres
    } finally {
      loading.value = false
      mostrarSelect.value = true // SIEMPRE muestra ContainerSelect después de la llamada
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
</style>
