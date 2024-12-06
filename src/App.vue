<template>
  <div class="container">

    <h3>
      ตัวสร้างเด็ค Pokémon TCG Pocket
    </h3>

    <DeckName />
    <DeckCards />
    <AddCards />
  </div>
</template>

<script setup>
import { onMounted, provide, ref } from 'vue';
import DeckName from './components/DeckName.vue';
import DeckCards from './components/DeckCards.vue';
import AddCards from './components/AddCards.vue';

const deck = ref({
  name: '',
  cards: []
})

const results = ref([])

provide('deck', deck)
provide('results', results)

onMounted(() => {
  if (localStorage.getItem('deck')) {
    deck.value = JSON.parse(localStorage.getItem('deck'))
  }
})
</script>

<style scoped></style>