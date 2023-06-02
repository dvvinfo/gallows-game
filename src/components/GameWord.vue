<template>
  <div class="word">
    <span v-for="(letter, i) in word" :key="i" class="letter">
      {{ correctLetters.includes(letter) ? letter : '' }}
    </span>
  </div>
</template>

<script setup lang="ts">
interface Word {
  word: string
  correctLetters: string[]
}
defineProps<Word>()
</script>

<style scoped></style>
