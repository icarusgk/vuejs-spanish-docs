<script setup>
import { ref } from 'vue'
const msg = ref('')
</script>

<template>
  <p>Componente actual: B</p>
  <span style="margin-right: 20px">El mensaje es: {{ msg }}</span>
  <input v-model="msg" />
</template>
