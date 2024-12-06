<template>
  <div>
    <div class="cards" v-if="showDeck">
      <ul>
        <li v-for="card in filterDeck" @click="getPokemon(card)">
          <img :src="getImage(card)" alt="" class="card">
          <div class="counter">{{ counter(card) }}</div>
        </li>
      </ul>
    </div>
    <div class="head">
      <h4>การ์ด {{ deck.cards.length }}/20</h4>
      <div class="showCard" @click="showDeck = !showDeck">
        <p v-if="showDeck">ซ่อนการ์ด</p>
        <p v-else>ดูการ์ด</p>
      </div>
    </div>
  </div>
</template>

<script setup>
import axios from 'axios';
import { computed, inject, ref } from 'vue';

const deck = inject('deck')
const results = inject('results')
const showDeck = ref(true)

const filterDeck = computed(() => {
  const cards = deck.value.cards;
  const uniq = [];

  cards.forEach(card => {
    if (!uniq.includes(card.cardID)) {
      uniq.push(card.cardID)
    }
  });

  return uniq
})

const getImage = (cardID) => {
  const set = cardID.split("_")[0]

  return `https://limitlesstcg.nyc3.digitaloceanspaces.com/pocket/${set}/${cardID}_EN.webp`
}

const counter = (cardID) => {
  const cards = deck.value.cards;
  return cards.filter(card => card.cardID == cardID).length
}

const getPokemon = (cardID) => {
  const set = cardID.split("_")
  axios.get(`https://pocket.limitlesstcg.com/api/dm/cards?q=${set[0]}~${Number(set[1])}&lang=en`).then(res => {
    console.log(res.data);
    results.value = res.data;
  })
}
</script>

<style scoped>
.cards {
  margin-top: 1em;
}

.cards ul {
  display: flex;
  flex-wrap: wrap;
  gap: .5em
}

li {
  width: 18%;
  position: relative;
}

img {
  width: 100%;
}

.counter {
  width: 100%;
  height: 20%;
  position: absolute;
  background-image: url(https://my.limitlesstcg.com/img/cardnum.png);
  background-repeat: no-repeat;
  background-position: center;
  background-size: contain;
  bottom: 3%;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 1.5em;
  font-weight: bold;
}

.head {
  display: flex;
  justify-content: space-between;
  border-bottom: 1px solid var(--secondary-button-bg);
  margin: 1em 0;
  padding-bottom: .5em;
}


@media only screen and (max-width: 768px) {
  .counter {
    font-size: 1em;
  }
}
</style>