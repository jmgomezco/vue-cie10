<template>
  <textarea
    ref="textArea"
    class="caja-texto"
    v-model="contenido"
    placeholder=""
    rows="1"
    @keydown.enter.prevent="enviarSiNoVacio"
  />
</template>

<script>
export default {
  name: 'CajaTexto',
  data() {
    return {
      contenido: ''
    }
  },
  mounted() {
    this.$refs.textArea.focus();
  },
  methods: {
    enviarSiNoVacio() {
      if (this.contenido.trim().length > 0) {
        this.$emit('enviar', this.contenido.trim());
        this.contenido = '';
      }
    }
  }
}
</script>

<style scoped>
.caja-texto {
    width: 100%;
    padding: 10px;
    border: 3px solid #6495ED;
    border-radius: 55px;
    font-family: "Courier New", Courier, monospace;
    font-size: clamp(15px, 3vw, 22px);
    text-align: left;
    box-sizing: border-box;
    color: #292929;
    background-color: #fff;
    margin-bottom: clamp(26px, 5vw, 38px);
    resize: none;           /* No permite expandir */
    outline: none;
    box-shadow: none;       /* Sin sombra */
}
.caja-texto:focus {
    border-color: #4169E1;
    box-shadow: none;       /* Sin sombra también en focus */
}
</style>
