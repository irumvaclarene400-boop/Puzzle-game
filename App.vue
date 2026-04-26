<script setup>
import { ref, onMounted } from 'vue'

// Game state
const size = ref(3) // 3x3 default (Easy)
const tiles = ref([])
const emptyIndex = ref(0)
const moves = ref(0)
const time = ref(0)

let timer = null
let startTime = 0

// Initialize game
function initGame() {
  const total = size.value * size.value
  tiles.value = [...Array(total).keys()]
  emptyIndex.value = total - 1

  shuffle()

  moves.value = 0
  time.value = 0
  startTime = Date.now()

  clearInterval(timer)
  timer = setInterval(() => {
    time.value = Math.floor((Date.now() - startTime) / 1000)
  }, 1000)
}

// Shuffle tiles
function shuffle() {
  tiles.value.sort(() => Math.random() - 0.5)
}

// Move tile
function moveTile(index) {
  const validMoves = [
    emptyIndex.value - 1,
    emptyIndex.value + 1,
    emptyIndex.value - size.value,
    emptyIndex.value + size.value
  ]

  if (validMoves.includes(index)) {
    ;[tiles.value[index], tiles.value[emptyIndex.value]] =
      [tiles.value[emptyIndex.value], tiles.value[index]]

    emptyIndex.value = index
    moves.value++

    checkWin()
  }
}

// Check win
function checkWin() {
  if (tiles.value.every((t, i) => t === i)) {
    clearInterval(timer)
    alert(`🎉 You won in ${moves.value} moves and ${time.value}s`)
  }
}

// Difficulty
function setDifficulty(level) {
  size.value = level === 'easy' ? 3 : level === 'medium' ? 4 : 5
  initGame()
}

onMounted(initGame)
</script>

<template>
  <div class="container">
    <h1>Puzzle Game</h1>

    <div class="info">
      ⏱ Time: {{ time }}s | 🔢 Moves: {{ moves }}
    </div>

    <div class="buttons">
      <button @click="setDifficulty('easy')">Easy</button>
      <button @click="setDifficulty('medium')">Medium</button>
      <button @click="setDifficulty('hard')">Hard</button>
      <button @click="initGame">Restart</button>
    </div>

    <div 
      class="grid" 
      :style="{ gridTemplateColumns: `repeat(${size}, 80px)` }"
    >
      <div
        v-for="(tile, index) in tiles"
        :key="index"
        class="tile"
        @click="moveTile(index)"
      >
        {{ tile !== tiles.length - 1 ? tile + 1 : '' }}
      </div>
    </div>
  </div>
</template>

<style>
.container {
  text-align: center;
  margin-top: 20px;
}

.info {
  margin: 10px 0;
}

.buttons button {
  margin: 5px;
  padding: 8px;
  cursor: pointer;
}

.grid {
  display: grid;
  gap: 5px;
  justify-content: center;
  margin-top: 20px;
}

.tile {
  width: 80px;
  height: 80px;
  background: #3498db;
  color: white;
  font-size: 20px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
}
</style>
