<template>
  <div class="loader" />
</template>

<script setup lang="ts"></script>

<style scoped>
.loader {
  position: static;
  width: 100%;
  height: 100%;
  min-height: 60px;
}

.loader::after {
  content: '';
  position: absolute;
  top: 30%;
  left: 44%;
  width: 20px;
  height: 20px;
  border: 3px solid #ccc;
  border-top-color: #333;
  border-radius: 50%;
  animation: spin 1s linear infinite;
}

@keyframes spin {
  to {
    transform: rotate(360deg);
  }
}
</style>
