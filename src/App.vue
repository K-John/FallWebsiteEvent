<template>
  <FallEvent :characters="characters"></FallEvent>
</template>

<script setup>
import FallEvent from './components/FallEvent.vue';

let characters = [
  {
    name: 'Pumpkin',
    username: 'pumpkin',
    image: '/src/assets/pumpkin.svg'
  },
  {
    name: 'Pumpkin',
    username: 'pumpkin',
    image: '/src/assets/pumpkin.svg'
  },
  {
    name: 'Pumpkin',
    username: 'pumpkin',
    image: '/src/assets/pumpkin.svg'
  }
];
</script>
