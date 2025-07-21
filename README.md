## Setup

Make sure to install dependencies:

```bash
# npm
npm install

# pnpm
pnpm install

# yarn
yarn install

# bun
bun install
```

## Development Server

Start the development server on `http://localhost:3000`:

```bash
# npm
npm run dev

# pnpm
pnpm dev

# yarn
yarn dev

# bun
bun run dev
```

## Notes

### Grid Representation

```javascript
let grid = [
  [0, 1, 0],
  [1, 1, 1],
  [0, 0, 0],
];
```

- 1 => living cells
- 0 => dead cells

### Rendering the Grid

- nested v-for to iterate over grid array and render element for each cell.
- apply dynamic class to style it differently (green for alive, white for dead).

### Game Logic

- takes current grid as input, return next state of the grid.
- for each cell, count its live neighbors (8 surrounding cells).
- RULES:
  - Underpopulation: A live cell with < 2 live neighbors dies.
  - Survival: A live cell with 2 or 3 live neighbors lives on to the next generation.
  - Overpopulation: A live cell with > 3 live neighbors dies.
  - Reproduction: A dead cell with exactly = 3 live neighbors becomes a live cell.
