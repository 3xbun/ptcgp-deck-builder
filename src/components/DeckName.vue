<template>
  <div>
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
    <div class="btn copy" @click="copyToClipboard">
      <i class="fa-light fa-copy"></i>
    </div>
  </div>
</template>

<script setup>
import { inject } from 'vue';

const deck = inject('deck')
const deckText = inject('deckText')

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

const copyToClipboard = () => {
  // console.log(deckText.value);
  navigator.clipboard.writeText(deckText.value);
}
</script>

<style scoped>
div {
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
  border: 2px solid var(--primary-text);
  color: var(--primary-text);
  background-color: transparent;
  width: 10%;
}
</style>