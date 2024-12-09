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
import { computed, onMounted, provide, ref } from 'vue';
import DeckName from './components/DeckName.vue';
import DeckCards from './components/DeckCards.vue';
import AddCards from './components/AddCards.vue';

const deck = ref({
  name: '',
  cards: []
})

const results = ref([])
const deckText = computed(() => {
  const d = deck.value
  const DeckName = d.name || "ไม่มีชื่อ"

  const pokemons = d.cards.filter(card => card.card_type == 'pokemon')
  const totalPkm = pokemons.length
  const txtPkm = formatter(pokemons)

  const trainers = d.cards.filter(card => card.card_type == 'trainer')
  const totalTrn = trainers.length
  const txtTrn = formatter(trainers)

  const text = `${DeckName}\n\nโปเกมอน: ${totalPkm}\n${txtPkm}\n\nเทรนเนอร์: ${totalTrn}\n${txtTrn}`
  return text
})

const formatter = (list) => {
  const cards = list.sort((a, b) => (a.card_type > b.card_type) ? 1 : ((b.card_type > a.card_type) ? -1 : 0)).sort((a, b) => (a.id > b.id) ? 1 : ((b.id > a.id) ? -1 : 0));

  const uniq = []
  let txt = ''

  cards.forEach(card => {
    if (uniq.some(c => c.id == card.id)) {
      uniq.filter(c => c.id == card.id)[0].amt += 1
    } else {
      card.amt = 1
      uniq.push(card)
    }
  });

  uniq.forEach(item => {
    txt += `${item.amt} ${item.name} ${item.set} ${item.number}\n`
  });

  return txt;
}

provide('deckText', deckText)
provide('deck', deck)
provide('results', results)

onMounted(() => {
  if (localStorage.getItem('deck')) {
    deck.value = JSON.parse(localStorage.getItem('deck'))
  }
})
</script>

<style scoped></style>