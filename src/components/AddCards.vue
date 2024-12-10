<template>
  <div>
    <h2>เพิ่มการ์ด</h2>
    <input type="text" placeholder="ค้นหาการ์ด" v-model="name">
    <div class="btn" @click="showFilter()">
      <p>
        ตัวกรอง
      </p>
      <i class="fa-light fa-chevron-up" v-if="isShowFilter"></i>
      <i class="fa-light fa-chevron-down" v-else></i>
    </div>
    <div class="filter" v-if="isShowFilter">
      <div class="animate__animated animate__faster animate__fadeInDown">
        <p>ประเภทการ์ด</p>
        <div class="types">
          <p class="btn" :class="{ active: filter.type == 'pokemon' }" @click="filter.type = 'pokemon'">โปเกมอน</p>
          <p class="btn" :class="{ active: filter.type == 'trainer' }" @click="filter.type = 'trainer'">เทรนเนอร์</p>
          <p class="btn" :class="{ active: filter.type == 'supporter' }" @click="filter.type = 'supporter'">
            ซัพพอร์ตเตอร์</p>
          <p class="btn" :class="{ active: filter.type == 'item' }" @click="filter.type = 'item'">ไอเท็ม</p>
        </div>
        <p>สี</p>
        <div class="colors">
          <img :class="{ active: filter.color == 'grass' }" @click="filter.color = 'grass'"
            src="https://pocket.untapped.gg/images/card-colors/grass.png">
          <img :class="{ active: filter.color == 'fire' }" @click="filter.color = 'fire'"
            src="https://pocket.untapped.gg/images/card-colors/fire.png">
          <img :class="{ active: filter.color == 'water' }" @click="filter.color = 'water'"
            src="https://pocket.untapped.gg/images/card-colors/water.png">
          <img :class="{ active: filter.color == 'lightning' }" @click="filter.color = 'lightning'"
            src="https://pocket.untapped.gg/images/card-colors/lightning.png">
          <img :class="{ active: filter.color == 'psychic' }" @click="filter.color = 'psychic'"
            src="https://pocket.untapped.gg/images/card-colors/psychic.png">
          <img :class="{ active: filter.color == 'fighting' }" @click="filter.color = 'fighting'"
            src="https://pocket.untapped.gg/images/card-colors/fighting.png">
          <img :class="{ active: filter.color == 'darkness' }" @click="filter.color = 'darkness'"
            src="https://pocket.untapped.gg/images/card-colors/darkness.png">
          <img :class="{ active: filter.color == 'metal' }" @click="filter.color = 'metal'"
            src="https://pocket.untapped.gg/images/card-colors/metal.png">
          <img :class="{ active: filter.color == 'dragon' }" @click="filter.color = 'dragon'"
            src="https://pocket.untapped.gg/images/card-colors/dragon.png">
          <img :class="{ active: filter.color == 'colorless' }" @click="filter.color = 'colorless'"
            src="https://pocket.untapped.gg/images/card-colors/colorless.png">
        </div>
        <div class="btns">
          <p class="btn" @click="searchWithFilter()">ค้นหาด้วยตัวกรอง</p>
          <p class="btn reset" @click="filter = {
            color: '',
            type: ''
          }">ล้างตัวกรอง</p>
        </div>
      </div>
    </div>

    <div class="results">
      <ul v-if="results.length > 0">
        <li v-for="item in results">
          <div class="overlay" v-if="counter(item)">
            {{ counter(item) }}/2
            <div class="control">
              <i class="fa-light fa-circle-minus" @click="removeCard(item)"></i>
              <i class="fa-light fa-circle-plus" @click="addCard(item)"></i>
            </div>
          </div>
          <img @click="addCard(item)"
            :src="`https://limitlesstcg.nyc3.digitaloceanspaces.com/pocket/${item.set}/${item.set}_${String(item.number).padStart(3, '0')}_EN.webp`"
            alt="" class="card">
        </li>
      </ul>
      <div class="errors" v-else>
        <p v-if="name">ไม่พบการค้นหา</p>
        <p v-else>ค้นหาชื่อการ์ดที่ต้องการ</p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { inject, provide, ref, watch } from 'vue';
import axios from 'axios';

const name = ref('')
const results = inject('results')
const deck = inject('deck')
const isShowFilter = ref(false)

const filter = ref({
  color: '',
  type: ''
})

const searchWithFilter = () => {
  const type = filter.value.type
  const color = filter.value.color

  let link = `https://pocket.limitlesstcg.com/api/dm/search?q=${name.value}%20`

  if (type || color) {
    link += 'type%3A'
  }

  type ? link += type : null
  type && color ? link += '%2B' : null
  color ? link += color : null

  axios.get(link).then(res => { results.value = res.data })
}

watch(name, (text) => {
  const link = `https://pocket.limitlesstcg.com/api/dm/search?q=${text}%20&lang=en`
  if (name.value) {
    axios.get(link).then(res => { results.value = res.data })
  } else {
    results.value = []
  }
})

const showFilter = () => {
  isShowFilter.value = !isShowFilter.value
}

const addCard = (card) => {
  const cards = deck.value.cards

  if (cards.length < 20) {
    if (cards.filter(c => c.name == card.name).length < 2) {
      const cardID = `${card.set}_${String(card.number).padStart(3, '0')}`
      card.cardID = cardID
      cards.push(card)
    }
  }
}

const removeCard = (card) => {
  const cards = deck.value.cards

  const index = cards.findIndex(item => item.cardID === card.cardID);

  if (index !== -1) {
    cards.splice(index, 1); // Remove one item at the found index
  }
}

const counter = (card) => {
  const cards = deck.value.cards;
  const cardID = `${card.set}_${String(card.number).padStart(3, '0')}`
  card.cardID = cardID
  return cards.filter(c => c.cardID == card.cardID).length
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
  overflow: hidden;
}

.types {
  display: flex;
  justify-content: space-between;
  flex-wrap: wrap;
  margin-bottom: 1em;
}

.types p {
  width: 49%;
  margin: 0 0 .5em;
  border: 2px solid var(--divider-color);
  background-color: transparent;
  color: var(--divider-color);
}

.types p.active {
  border: 2px solid transparent;
  background-color: var(--primary-button-bg);
  color: var(--primary-button-text);
}

.colors {
  display: flex;
  flex-wrap: wrap;
  gap: 1em;
}

.colors img {
  height: 2em;
  width: auto;
  filter: grayscale(.8);
}

.colors img.active {
  scale: 1.2;
  filter: none;
}

.btns {
  display: flex;
  gap: .5em;
}

.btns .btn {
  width: 100%;
  justify-content: center;
}

.reset {
  border: 2px solid var(--error-color);
  color: #ffcccc;
  background-color: transparent;
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

.errors {
  margin-top: 1em;
  text-align: center;
  color: var(--divider-color);
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