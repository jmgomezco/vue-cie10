<template>
  <a
    class="sobre-link"
    href="mailto:nhug@nhug.ai"
    target="_blank"
    rel="noopener noreferrer"
    aria-label="Enviar correo"
  >
    <svg class="envelope-icon" viewBox="0 0 28 22" fill="none" xmlns="http://www.w3.org/2000/svg">
      <rect
        x="1"
        y="1"
        width="26"
        height="20"
        rx="3"
        stroke="#354e7b"
        stroke-width="2"
        fill="none"
      />
      <polyline
        points="1,3 14,14 27,3"
        stroke="#354e7b"
        stroke-width="2"
        fill="none"
      />
    </svg>
  </a>
</template>

<script>
export default {
  name: 'Sobre'
}
</script>

<style scoped>
.sobre-link {
  display: inline-block;
  cursor: pointer;
}

.sobre-link .envelope-icon {
  width: 40px;
  height: 32px;
  transition: stroke 0.2s, color 0.2s;
}

.sobre-link:hover .envelope-icon rect,
.sobre-link:hover .envelope-icon polyline {
  stroke: #6495ED; /* celeste */
}
</style>
