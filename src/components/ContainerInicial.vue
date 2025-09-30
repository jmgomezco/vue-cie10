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
    align-items: center;        
    justify-content: flex-start;
    width: 100%;
    max-width: 500px;
    min-height: 100vh;
    margin: 50px 0 0 300px;   /* Desplaza el contenedor 300px a la derecha */
    padding: 0 14px;
    box-sizing: border-box;
    font-family: monospace;
    position: relative;
}

/* Para pantallas pequeñas, ajusta el desplazamiento */
@media (max-width: 600px) {
  .container-inicial {
    max-width: 100%;
    padding: 0 8px;
    margin: 24px 0 0 8px;    /* Menor desplazamiento en móviles */
  }
}
</style>
