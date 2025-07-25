<template>
  <div>
    <h1>Game of Life</h1>
    <div class="controls">
      <button @click="startSimulation" :disabled="isRunning" class="button">Start</button>
      <button @click="stopSimulation" :disabled="!isRunning" class="button">Stop</button>
      <button @click="resetGrid" :disabled="isRunning" class="button">Reset</button>
      <button @click="randomizeGrid" :disabled="isRunning" class="button">Randomize</button>
    </div>

    <div class="grid-container">
      <div v-for="(row, rowIndex) in grid" :key="rowIndex">
        <div v-for="(cell, colIndex) in row" :key="colIndex" :class="['cell', { 'cell-alive': cell === 1 }]"
          @click="toggleCell(rowIndex, colIndex)">
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
const gridSize = 30;  // 30x30 grid
const cellSize = '15px';  // 15px in CSS
const grid = ref([]);
const isRunning = ref(false);
let simulationInterval = null;

// Initialize grid
const initializeGrid = (random = false) => {
  // Create new 2D array and fill it with zeros (dead cells)
  const newGrid = Array(gridSize).fill(0).map(() => Array(gridSize).fill(0));

  // If random
  if (random) {
    newGrid.forEach((row, rowIndex) => {
      row.forEach((_, colIndex) => {
        newGrid[rowIndex][colIndex] = Math.random() < 0.3 ? 1 : 0;  // 30% is alive
      });
    });
  }

  grid.value = newGrid;
};

// Toggling Cell
const toggleCell = (row, col) => {
  // create deep copy of current grid.value
  let tempGrid = structuredClone(toRaw(grid.value));
  // access cell at row,col in copied grid, toggle its value 0-1
  tempGrid[row][col] = tempGrid[row][col] === 1 ? 0 : 1;
  // assign copied grid to original
  grid.value = tempGrid
}

// Count Neighbors
const countLiveNeighbors = (row, col) => {
  // counter of live neighbors
  let liveNeighbors = 0;
  // define array of directions represent 8 possible offsets for neighbors
  const directions = [
    [-1, 0],  // top
    [-1, 1],  // topright
    [0, 1],  // right
    [1, 1],  // bottomright
    [1, 0],  // bottom
    [1, -1],  // bottomleft
    [0, -1],  // left
    [-1, -1],  // topleft
  ];
  // loop through directions
  directions.forEach(([newRow, newCol]) => {
    // calculate neighbors newRow and newCol by adding direction offsets to current row and col
    let neighborsRows = row + newRow;
    let neighborsCols = col + newCol;

    // [newRow, newCol] must be within gridSize boundary, if exceed ignore them
    if ((neighborsRows >= 0 && neighborsRows < gridSize) && (neighborsCols >= 0 && neighborsCols < gridSize)) {
      // if within bounds, add its value to liveNeighbors
      liveNeighbors += grid.value[neighborsRows][neighborsCols];
    }
  });

  // return liveNeighbors count
  return liveNeighbors;
}

// calculate Next Generation
const calculateNextGeneration = () => {
  // create new empty 2D array (store state of next gen)
  const newGrid = Array(gridSize).fill(0).map(() => Array(gridSize).fill(0));

  // nested for loop to iterate every cell (row, col) of current grid.value
  grid.value.forEach((row, rowIndex) => {
    row.forEach((_, colIndex) => {

      // get currentCellState
      let currentCellState = grid.value[rowIndex][colIndex];
      // call countLiveNeighbors
      let liveNeighbors = countLiveNeighbors(rowIndex, colIndex);

      // apply CONWAY'S RULES
      if (currentCellState === 1) {  // live cell

        if (liveNeighbors < 2) { // Underpopulation
          newGrid[rowIndex][colIndex] = 0;
        } else if (liveNeighbors == 2 || liveNeighbors == 3) {  // Survival
          newGrid[rowIndex][colIndex] = 1;
        } else if (liveNeighbors > 3) {  // Overpopulation
          newGrid[rowIndex][colIndex] = 0;
        }

      } else {
        if (liveNeighbors === 3) { // dead cell
          newGrid[rowIndex][colIndex] = 1; // Reproduction
        }
      }
    });
  });

  grid.value = newGrid;
};

// Start Simulation
const startSimulation = () => {
  isRunning.value = true;
  simulationInterval = setInterval(calculateNextGeneration, 1000);
};

// Stop Simulation
const stopSimulation = () => {
  isRunning.value = false;
  clearInterval(simulationInterval);
  simulationInterval = null;
}

// Randomize Grid
const randomizeGrid = () => {
  stopSimulation();
  initializeGrid(true);
}

// Reset Grid
const resetGrid = () => {
  stopSimulation();
  initializeGrid();
}

onMounted(() => {
  initializeGrid();
});

onUnmounted(() => {
  stopSimulation();
})
</script>

<style scoped>
.grid-container {
  display: flex;
  border: 1px solid;
  width: fit-content;
  margin: 20px auto;
}

.cell {
  /* cell-dead*/
  width: v-bind(cellSize);
  height: v-bind(cellSize);
  border: 2px solid;
  background-color: #f0f0f0;
  cursor: pointer;
}

.cell-alive {
  background-color: greenyellow;
}

.controls {
  margin-top: 20px;
  text-align: center;
}

.button {
  cursor: pointer;
}
</style>