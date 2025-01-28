<template>
  <main>
    <TheSuggest v-model="suggestInputValue" />
  </main>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import TheSuggest from '@/components/TheSuggest.vue'

const suggestInputValue = ref('')
</script>

<style scoped></style>
