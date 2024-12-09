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
    <div class="btn success" v-if="isCopied">
      <i class="fa-thin fa-circle-check"></i>
    </div>
    <div class="btn copy" v-else @click="copyToClipboard">
      <i class="fa-light fa-copy"></i>
    </div>
  </header>
</template>

<script setup>
import { inject, ref } from 'vue';

const deck = inject('deck')
const deckText = inject('deckText')
const isCopied = ref(false)

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
  navigator.clipboard.writeText(deckText.value);
  isCopied.value = true
  setTimeout(() => {
    isCopied.value = false
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
}

.success {
  width: 10%;
  border: 2px solid transparent;
  background-color: #59b259;
  color: inherit;
}
</style>