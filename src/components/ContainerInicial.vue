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
    width: 95%;
    max-width: 900px;            /* anchura  */
    min-height: 100vh;
    margin: 40px auto 0 auto; /* En pantallas grandes: margen superior de 40px */
    padding: 0 14px;
    box-sizing: border-box;
    font-family: monospace;
    position: relative;
    gap: 50px; /* <-- AGREGA ESTA LÍNEA AQUÍ */
}

  @media (max-width: 600px) {
  .container-select {
    margin: 16px auto 0 auto; /* centra horizontalmente */
    max-width: 100vw;         /* evita desbordes */
    padding-left: 8px;
    padding-right: 8px;
    box-sizing: border-box;
  }

}

</style>
