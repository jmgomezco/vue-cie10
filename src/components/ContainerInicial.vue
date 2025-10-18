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
  align-items: flex-start;
  justify-content: flex-start;
  width: 95%;
  max-width: 900px;
  min-height: 100vh;
  margin: 40px auto 0 auto;
  padding: 0 14px;
  box-sizing: border-box;
  font-family: monospace;
  position: relative;
  gap: 50px;
}

/* Media query corregida: antes apuntaba a .container-select por error */
@media (max-width: 600px) {
  .container-inicial {
    margin: 16px auto 0 auto;
    max-width: 100%;
    padding-left: 8px;
    padding-right: 8px;
    box-sizing: border-box;
    width: 95%;
    overflow-x: hidden; /* última defensa contra desbordes */
  }
}
</style>
