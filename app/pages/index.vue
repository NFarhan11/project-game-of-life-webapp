<template>
  <div>
    <h1>Game of Life</h1>
    <div class="controls">
      <button @click="startSimulation" :disabled="isRunning">Start</button>
      <button @click="stopSimulation" :disabled="!isRunning">Stop</button>
      <button @click="resetGrid" :disabled="isRunning">Reset</button>
    </div>

    <div class="grid-container">
      <div v-for="(row, rowIndex) in grid" :key="rowIndex">
        <div v-for="(cell, colIndex) in row" :key="colIndex" class="cell">
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
const gridSize = 30;  // 30x30 grid
const cellSize = 15;  // 15px in CSS
const grid = ref([]);
const isRunning = ref(false);

// Initialize grid
const initializeGrid = () => {
  // Create new 2D array and fill it with zeros (dead cells)
  const newGrid = Array(gridSize).fill(0).map(() => Array(gridSize).fill(0));
  grid.value = newGrid;
};

onMounted(() => {
  initializeGrid();
});
</script>

<style scoped>
.grid-container {
  display: flex;
  border: 1px solid;
  width: fit-content;
  margin: 20px auto;
}

.cell {
  width: v-bind(cellSize + 'px');
  height: v-bind(cellSize + 'px');
  border: 2px solid;
  background-color: #f0f0f0;
  cursor: pointer;
}

.controls {
  margin-top: 20px;
  text-align: center;
}
</style>