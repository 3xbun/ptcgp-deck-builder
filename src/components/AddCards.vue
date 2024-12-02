<template>
  <div>
    <h2>เพิ่มการ์ด</h2>
    <input type="text" placeholder="ค้นหาการ์ด" v-model="name">
    <div class="btn" @click="showFilter = !showFilter">
      <p>
        ตัวกรอง
      </p>
      <i class="fa-light fa-chevron-up" v-if="showFilter"></i>
      <i class="fa-light fa-chevron-down" v-else></i>
    </div>
    <div class="filter" v-if="showFilter">
      <p>กำลังพัฒนา</p>
    </div>

    <div class="results">
      <ul>
        <li v-for="item in results">
          <div class="overlay" v-if="counter(`${item.set}_${String(item.number).padStart(3, '0')}`)">
            {{ counter(`${item.set}_${String(item.number).padStart(3, '0')}`) }}/2
            <div class="control">
              <i class="fa-light fa-circle-minus"
                @click="removeCard(`${item.set}_${String(item.number).padStart(3, '0')}`)"></i>
              <i class="fa-light fa-circle-plus"
                @click="addCard(item.name, `${item.set}_${String(item.number).padStart(3, '0')}`)"></i>
            </div>
          </div>
          <img @click="addCard(item.name, `${item.set}_${String(item.number).padStart(3, '0')}`)"
            :src="`https://limitlesstcg.nyc3.digitaloceanspaces.com/pocket/${item.set}/${item.set}_${String(item.number).padStart(3, '0')}_EN.webp`"
            alt="" class="card">
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import { inject, ref, watch } from 'vue';
import axios from 'axios';

const showFilter = ref(false)
const name = ref('')
const results = ref([])
const deck = inject('deck')

watch(name, (text) => {
  const link = `https://pocket.limitlesstcg.com/api/dm/search?q=${text}%20&lang=en`
  if (name.value) {
    axios.get(link).then(res => { results.value = res.data })
  } else {
    results.value = []
  }
})

const addCard = (name, cardID) => {
  const cards = deck.value.cards

  if (cards.length < 20) {
    cards.filter(card => card.name == name).length < 2 ? cards.push({ name: name, cardID: cardID }) : null
  }
}

const removeCard = (cardID) => {
  const cards = deck.value.cards

  const index = cards.findIndex(item => item.cardID === cardID);

  console.log(index);
  if (index !== -1) {
    cards.splice(index, 1); // Remove one item at the found index
  }
}

const counter = (cardID) => {
  const cards = deck.value.cards
  return cards.filter(card => card.cardID == cardID).length
}
</script>

<style scoped>
input {
  background-color: var(--primary-text);
  color: var(--primary-button-text);
  width: 100%;
}

.btn {
  margin-top: 1em;
  display: flex;
  justify-content: space-between;
  align-items: center;
  background-color: var(--secondary-button-bg);
  color: var(--secondary-button-text);
}

.filter {
  background-color: black;
  padding: 1em;
}

.results ul {
  margin-top: 1em;
  display: flex;
  flex-wrap: wrap;
  gap: .5em;
  justify-content: space-around;
}

li {
  width: 30%;
  position: relative;
}

img {
  width: 100%;
}

.overlay {
  position: absolute;
  width: 100%;
  height: 100%;
  background: linear-gradient(to bottom, rgba(0, 0, 0, .8), rgba(0, 0, 0, 1));
  font-size: 5em;
  font-weight: bold;
  display: flex;
  justify-content: center;
  align-items: center;
}

.control {
  position: absolute;
  justify-content: center;
  display: flex;
  text-align: center;
  width: 100%;
  font-size: 2rem;
  bottom: 0;
  border-radius: .5em;
  overflow: hidden;
  gap: .25rem;
}

.control i {
  width: 100%;
  padding: 1rem;
  background-color: var(--secondary-button-bg);
}

@media only screen and (max-width: 768px) {
  .overlay {
    font-size: 3rem;
  }

  .control i {
    font-size: 1.5rem;
    padding: .5rem;
  }
}
</style>