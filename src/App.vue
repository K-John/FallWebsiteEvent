<template>
  <FallEvent :characters="characters"></FallEvent>
</template>

<script setup>
import FallEvent from './components/FallEvent.vue';

let characters = [
  {
    name: 'Kendall J.',
    username: 'kendall.johnson',
    image: 'https://techlink.cmcss.net/Login/GetPhoto?PersonId=6323'
  },
  {
    name: 'Emily D.',
    username: 'emily.demorest',
    image: 'https://techlink.cmcss.net/Login/GetPhoto?PersonId=5739'
  },
  {
    name: 'Tiffany O.',
    username: 'tiffany.owen',
    image: 'https://techlink.cmcss.net/Login/GetPhoto?PersonId=3'
  },
  {
    name: 'Eric S.',
    username: 'eric.salyer',
    image: 'https://techlink.cmcss.net/Login/GetPhoto?PersonId=5'
  },
  {
    name: 'Joe W.',
    username: 'joe.williams',
    image: 'https://techlink.cmcss.net/Login/GetPhoto?PersonId=2'
  },
  {
    name: 'Pumpkin',
    username: 'pumpkin',
    image: '/src/assets/pumpkin.svg'
  }
];
</script>
