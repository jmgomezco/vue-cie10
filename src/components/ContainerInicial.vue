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
    max-width: 900px;
    min-height: 100vh;
    margin: 32px auto 0;
    padding: 0 14px;
    box-sizing: border-box;
    font-family: monospace;
}
</style>
