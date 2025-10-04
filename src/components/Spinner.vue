<template>
  <div class="spinner"></div>
</template>

<script setup>
// No logic required for simple spinner
</script>

<style scoped>
.spinner {
    border: 8px solid #f3f3f3;
    border-top: 8px solid #6495ED;
    border-radius: 50%;
    width: 60px;
    height: 60px;
    animation: spin 1.2s linear infinite;
    margin: 14px auto;
    display: block;
    align-self: center;
}

@keyframes spin {
    0% { transform: rotate(0deg); }
    100% { transform: rotate(360deg); }
}
</style>
