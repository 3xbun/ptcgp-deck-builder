<template>
  <div>
    <input type="text" placeholder="ชื่อเด็ค" v-model="deck.name">
    <p class="btn reset" @click="reset()">รีเซ็ต</p>
    <p class="btn save" @click="save()">บันทึก</p>
  </div>
</template>

<script setup>
import { inject } from 'vue';

const deck = inject('deck')

const reset = () => {
  deck.value = {
    name: '',
    cards: []
  }
}

const save = () => {
  localStorage.setItem('deck', JSON.stringify(deck.value))
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
</style>