<template>
  <div>
    <h1>Decoder</h1>
    <div class="input">
      <input type="text" placeholder="Add link" v-model="l">
      <i @click="links.push(l)" class="fa-regular fa-plus"></i>
    </div>
    <div v-for="(link, index) in links" class="links">
      <div class="item">
        {{ decoder(link).name ? decoder(link).name : "ไม่มีชื่อ" }}
        <i class="fa-regular fa-copy" @click="createText(decoder(link))"></i>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
const links = ref([])
const l = ref("")

const decoder = (link) => {
  const d = link.split("share=")[1]

  const replacer = [
    "card_type:t",
    "pokemon:pk",
    "set:s",
    "number:no",
    "name:n",
    "trainer:tr",
    "cardID:ci",
    "cards:c"
  ]

  let dd = atob(d)

  replacer.forEach(item => {
    const word = item.split(":")[0]
    const key = item.split(":")[1]

    dd = dd.replaceAll(`"${key}"`, `"${word}"`)
  })

  dd = JSON.parse(dd)

  dd.cards.forEach(card => {
    card.cardID = `${card.set}_${String(card.number).padStart(3, '0')}`
  })

  return dd

  // localStorage.setItem('deck', JSON.stringify(dd))
  // window.location.href = window.location.href.split("share=")[0]
}

const createText = deck => {
  const d = deck
  const DeckName = d.name || "ไม่มีชื่อ"

  const pokemons = d.cards.filter(card => card.card_type == 'pokemon')
  const totalPkm = pokemons.length
  const txtPkm = formatter(pokemons)

  const trainers = d.cards.filter(card => card.card_type == 'trainer')
  const totalTrn = trainers.length
  const txtTrn = formatter(trainers)

  const text = `${DeckName}\n\nโปเกมอน: ${totalPkm}\n${txtPkm}\n\nเทรนเนอร์: ${totalTrn}\n${txtTrn}`

  navigator.clipboard.writeText(text);
  // return text
}

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

</script>

<style scoped>
.input {
  display: flex;
  align-items: center;
  gap: .5em;
  margin-bottom: 1em;
}

input {
  width: 100%;
}

i {
  color: white;
  border-radius: .5em;
  border: 1px solid white;
  padding: .5em;
}

.item {
  margin-bottom: 1em;
}
</style>