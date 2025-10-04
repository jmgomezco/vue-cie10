<template>
  <div class="container-inicial">
    <slot />
  </div>
</template>

<script>
export default {}
</script>

<style scoped>
.container-inicial {
    display: flex;
    flex-direction: column;
    align-items: flex-start;      /* Justifica el contenido a la izquierda */
    justify-content: flex-start;
    width: 100%;
    max-width: 900px;            /* anchura  */
    min-height: 100vh;
    margin: 60px auto 0 auto;     /* Centra el contenedor horizontalmente */
    padding: 0 14px;
    box-sizing: border-box;
    font-family: monospace;
    position: relative;
    gap: 20px; /* <-- AGREGA ESTA LÍNEA AQUÍ */
}

@media (max-width: 600px) {
  .container-inicial {
    max-width: 100%;
    padding: 0 8px;
    margin: 24px auto 0 auto;    /* También centrado en móvil */
  }
}
</style>
