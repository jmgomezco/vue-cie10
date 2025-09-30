<template>
  <div class="spinner">
    <svg class="spinner-svg" viewBox="0 0 100 100">
      <circle
        class="spinner-bg"
        cx="50"
        cy="50"
        r="40"
        fill="none"
        stroke="#b0b0b0"
        stroke-width="10"
      />
      <circle
        class="spinner-path"
        cx="50"
        cy="50"
        r="40"
        fill="none"
        stroke="#6495ED"
        stroke-width="10"
        stroke-linecap="round"
      />
    </svg>
  </div>
</template>

<script>
export default {
  name: "Spinner"
}
</script>

<style scoped>
.spinner {
  display: inline-block;
  width: 96px;
  height: 96px;
}

.spinner-svg {
  animation: spinner-rotate 2s linear infinite; /* Doble de lento (antes: 1s) */
  width: 100%;
  height: 100%;
}

.spinner-bg {
  opacity: 0.3;
}

.spinner-path {
  stroke-dasharray: 180, 300;
  stroke-dashoffset: 0;
  animation: spinner-dash 1.5s ease-in-out infinite;
}

@keyframes spinner-rotate {
  100% {
    transform: rotate(360deg);
  }
}

@keyframes spinner-dash {
  0% {
    stroke-dasharray: 2, 300;
    stroke-dashoffset: 0;
  }
  50% {
    stroke-dasharray: 180, 300;
    stroke-dashoffset: -70;
  }
  100% {
    stroke-dasharray: 180, 300;
    stroke-dashoffset: -248;
  }
}
</style>
