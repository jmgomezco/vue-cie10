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
    margin: 40px auto 0 auto; /* En pantallas grandes: margen superior de 40px */
    padding: 0 14px;
    box-sizing: border-box;
    font-family: monospace;
    position: relative;
    gap: 80px; /* <-- AGREGA ESTA LÍNEA AQUÍ */
}

@media (max-width: 600px) {
  .container-inicial {
    margin: 16px auto 0 auto; /* En móviles: margen superior de 16px */
  }
}

</style>
