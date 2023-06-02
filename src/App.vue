<template>
  <div class="wrapper">
    <GameHeader />
    <div class="game-container">
      <GameFigure :wrong-letters-count="wrongLetters.length" />
      <GameWrongLetters :wrong-letters="wrongLetters" />
      <GameWord :word="word" :correct-letters="correctLetters" />
    </div>
    <GamePopup :word="word" ref="popup" @restart="restart" />
    <GameNotification ref="notification" />
  </div>
</template>

<script setup lang="ts">
import { ref, computed, watch, onMounted } from 'vue'
import { getRandomName } from './api/getRandomName'

import GameHeader from './components/GameHeader.vue'
import GameFigure from './components/GameFigure.vue'
import GameWrongLetters from './components/GameWrongLetters.vue'
import GameWord from './components/GameWord.vue'
import GamePopup from './components/GamePopup.vue'
import GameNotification from './components/GameNotification.vue'

const word = ref('')
const letters = ref<string[]>([])
const notification = ref<InstanceType<typeof GameNotification> | null>(null)
const popup = ref<InstanceType<typeof GamePopup> | null>(null)

const getRandomWord = async () => {
  try {
    const name = await getRandomName()
    word.value = name.toLowerCase()
  } catch (error) {
    console.log(error)
    word.value = ''
  }
}

onMounted(() => {
  getRandomWord()
})

const restart = async () => {
  await getRandomWord()
  letters.value = []
  popup.value?.close()
}

const correctLetters = computed(() => letters.value.filter((letter) => word.value.includes(letter)))
const wrongLetters = computed(() => letters.value.filter((letter) => !word.value.includes(letter)))
const isLose = computed(() => wrongLetters.value.length === 6)
const isWin = computed(() => [...word.value].every((x) => correctLetters.value.includes(x)))

watch(wrongLetters, () => {
  if (isLose.value) {
    popup.value?.open('lose')
  }
})
watch(correctLetters, () => {
  if (isWin.value) {
    popup.value?.open('win')
  }
})

window.addEventListener('keydown', ({ key }) => {
  if (isLose.value || isWin.value) {
    return
  }
  if (letters.value.includes(key)) {
    notification.value?.open()
    setTimeout(() => {
      notification.value?.close()
    }, 2000)
    return
  }
  if (/[а-яА-ЯёЁ]/.test(key)) {
    letters.value.push(key.toLowerCase())
  }
})
</script>

<style scoped></style>
