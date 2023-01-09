<style>
body {
  background: black;
}
.square {
  padding: 2vw;
  margin: 0px;
  border: 1px solid rgba(255, 255, 255, 0.05);
}
.highlightOne {
  border: 1px solid rgba(0, 255, 255, 0.2);
}

.highlightTwo {
  border: 1px solid rgba(0, 255, 255, 0.5);
}

.highlightThree {
  border: 1px solid rgba(0, 255, 255, 1);
}

.back {
  overflow: hidden;
}
</style>

<template>
  <div class="back grid">
    <div v-for="(list, y) in weights" :key="`Row${y}`" class="flex flex-row">
      <div v-for="(ele, x) in list" :key="`Row${y}Col${x}`" class="row">
        <div
          class="square"
          :class="{
            highlightOne: weights[y][x] == 1,
            highlightTwo: weights[y][x] == 2,
            highlightThree: weights[y][x] == 3,
          }"
          @mouseenter="handleMouseEnter(y, x)"
        />
      </div>
    </div>
  </div>
</template>

<script setup>
// Constants for rendering
const delay = 200;
const X_MAX = 24;
const Y_MAX = 12;
const probabilities = [1, 0.6, 0.4];

// Functions to handle mouse events
function handleMouseEnter(y, x) {
  setWeight(y, x, 3);
  setTimeout(() => {
    setWeight(y, x, 2);
  }, delay);
  setTimeout(() => {
    setWeight(y, x, 1);
  }, delay * 2);
  setTimeout(() => {
    setWeight(y, x, 0);
  }, delay * 3);
}

function setWeight(y, x, w) {
  weights.value[y][x] = w;
  if (w === 0 || w === 1 || w === 3) return;

  for (let j = Math.max(0, y - 2); j <= Math.min(y + 2, Y_MAX - 1); j++) {
    for (let i = Math.max(0, x - 2); i <= Math.min(x + 2, X_MAX - 1); i++) {
      const distance = Math.abs(i - x) + Math.abs(j - y);
      if (shouldChange(probabilities[distance])) {
        if (3 - distance <= w) {
          weights.value[j][i] = 3 - distance;
          setTimeout(() => {
            setWeight(j, i, 0);
          }, delay * distance);
        }
      }
    }
  }
}

function shouldChange(p) {
  return Math.random() < p;
}

// Initialise the weights grid
const weights = ref([]);
for (let y = 0; y < Y_MAX; y++) {
  weights.value.push([]);
  for (let x = 0; x < X_MAX; x++) weights.value[y].push(0);
}
</script>
