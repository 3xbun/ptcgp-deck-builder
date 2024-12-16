<template>
  <header>
    <input type="text" placeholder="ชื่อเด็ค" v-model="deck.name">
    <div class="btn reset" @click="reset()">
      <p>
        รีเซ็ต
      </p>
      <i class="fa-light fa-arrow-rotate-left"></i>
    </div>
    <div class="btn save" @click="save()">
      <p>
        บันทึก
      </p>
      <i class="fa-light fa-floppy-disk"></i>
    </div>
    <div class="btn success animate__animated animate__rubberBand" v-if="isCopied">
      <i class="fa-thin fa-circle-check"></i>
    </div>
    <div class="btn copy" v-else @click="share()"><i class="fa-light fa-share-from-square"></i>
      <div class="dropdown" v-if="showDropDown">
        <p @click="shareAsText">คัดลอกข้อความ</p>
        <hr>
        <p @click="shareAslink">คัดลอกลิงก์</p>
      </div>
    </div>
    <div class="notification" v-if="noti">
      <p class="animate__animated animate__bounceInUp">
        {{ noti }}คัดลอกสำเร็จ!
        <i class="fa-light fa-clipboard-check"></i>
      </p>
    </div>
  </header>
</template>

<script setup>
import { inject, ref } from 'vue';

const deck = inject('deck')
const deckText = inject('deckText')
const isCopied = ref(false)
const showDropDown = ref(false)
const noti = ref('')

const reset = () => {
  deck.value = {
    name: '',
    cards: []
  }

  localStorage.setItem('deck', JSON.stringify(deck.value))
}

const save = () => {
  localStorage.setItem('deck', JSON.stringify(deck.value))
}

const share = () => {
  showDropDown.value = !showDropDown.value
}

const shareAslink = () => {
  const dd = JSON.parse(JSON.stringify(deck.value));

  dd.cards.forEach(card => {
    delete card.cardID
    delete card.special
  })
  let d = JSON.stringify(dd);

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

  replacer.forEach(item => {
    const word = item.split(":")[0]
    const key = item.split(":")[1]
    d = d.replaceAll(`"${word}"`, `"${key}"`)
  });

  const enc = btoa(d)

  showNotification('ลิงก์')
  navigator.clipboard.writeText(window.location.href + "share=" + enc);
}

const shareAsText = () => {
  showNotification('ข้อความ')
  navigator.clipboard.writeText(deckText.value);
  console.log(showDropDown.value);
}

const showNotification = (value) => {
  noti.value = value
  setTimeout(() => {
    noti.value = ""
  }, 2000);
}
</script>

<style scoped>
header {
  display: flex;
  margin-top: 1em;
  gap: .5em;
}

input {
  width: 60%;
  border-radius: .5em;
}

.btn {
  width: 20%;
  text-align: center;
  display: flex;
  gap: .2em;
  align-items: center;
  justify-content: center;
  border-radius: .5em;
}

.reset {
  border: 2px solid var(--error-color);
  color: #ffcccc;
  background-color: transparent;
}

.save {
  border: 2px solid transparent;
  background-color: var(--primary-button-bg);
  color: var(--primary-button-text);
}

.copy {
  background-color: transparent;
  width: 10%;
  border: 2px solid var(--primary-text);
  color: var(--primary-text);
  position: relative
}

.success {
  width: 10%;
  border: 2px solid transparent;
  background-color: #59b259;
  color: inherit;
}

.dropdown {
  position: absolute;
  margin-top: .5em;
  top: 100%;
  right: 0;
  width: 10em;
  background-color: var(--card-area-color);
  border-radius: .5em;
  z-index: 99;
}

hr {
  border: 0;
  border-top: 1px solid var(--divider-color);
}

.dropdown p {
  text-align: left;
  padding: .5em;
}

.dropdown p:hover {
  background-color: var(--divider-color);
}

.notification {
  position: fixed;
  bottom: 0;
  height: 20vh;
  left: 0;
  right: 0;
  text-align: center;
  overflow: hidden;
  display: flex;
  justify-content: center;
  align-items: center;
}

.notification p {
  width: max-content;
  margin: auto;
  padding: .5em;
  background-color: #59b259;
  filter: opacity(.9);
  border-radius: .5em;
}
</style>