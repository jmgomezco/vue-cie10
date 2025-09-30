<template>
  <div class="marca">
    {{ textoMarca }}
  </div>
</template>

<script>
export default {
  props: {
    textoMarca: {
      type: String,
      default: "Mi Marca"
    }
  }
}
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@900&display=swap');
.marca {
  font-family: 'Orbitron', Arial, sans-serif;
  font-size: clamp(16px, 4vw, 30px);
  font-weight: 900;
  color: #fff;
  background: #000;
  padding: 10px 20px;
  line-height: 1.3;
  letter-spacing: 1.2px;
  text-align: center;
  width: fit-content;
  min-width: 120px;
  box-sizing: border-box;
  display: flex;
  align-items: center;
 justify-content: center;

  /* Posicionamiento absoluto respecto al contenedor */
/* Posicionamiento absoluto respecto al contenedor */
position: absolute;
left: 0;
top: 56px;         /* Mayor separación vertical del borde superior */
margin-left: 14px; /* Igual que el padding izquierdo del contenedor */
z-index: 10;
}

/* Ajustes responsivos para pantallas pequeñas */
@media (max-width: 600px) {
  .marca {
    top: 32px;     /* Mayor separación vertical en móviles también */
    margin-left: 8px;
    min-width: 80px;
    font-size: clamp(12px, 7vw, 22px);
    padding: 6px 10px;
  }
}
</style>
