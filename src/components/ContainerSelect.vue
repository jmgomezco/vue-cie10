<template>
  <div class="container-select">
    <Marca textoMarca="NhugAi" />
  </div>
</template>

<script setup>
import Marca from './Marca.vue'
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

@media (max-width: 600px) {
  .container-select {
    max-width: 100%;
    padding: 0 8px;
    margin: 24px auto 0 auto;
  }
}
</style>
