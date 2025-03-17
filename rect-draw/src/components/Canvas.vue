<template>
    <v-container class="canvas-container">
      <Rect 
        v-for="n in count" 
        :key="n" 
        width="100" 
        height="100" 
        color="blue" 
      />
    </v-container>
  </template>
  
  <script setup lang="ts">
  import Rect from './Rect.vue';
  import { ref } from 'vue';
  
  const count = ref(5); // rect count
  </script>
  
  <style scoped>
  .canvas-container {
    display: flex;
    flex-wrap: nowrap;
    justify-content: flex-start;
    align-items: flex-start;
    width: 100%;
    gap: 5px;
  }
  </style>