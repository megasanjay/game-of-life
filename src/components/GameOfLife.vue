<template>
  <div class="w-screen h-screen">
    <canvas
      ref="canvas"
      class="bg-white canvas"
      :width="width"
      :height="height"
    ></canvas>
  </div>
</template>

<script>
export default {
  name: "GameOfLife",
  data: function () {
    return {
      canvas: null,
      ctx: null,
      width: window.innerWidth,
      height: window.innerHeight,
      grid: [],
      cellSize: 30,
      path: null,
      scope: null,
      canvasId: "canvas-one",
    };
  },
  methods: {
    draw() {
      this.ctx.clearRect(0, 0, this.width, this.height);
      for (let row = 0; row < this.grid.length; row++) {
        for (let column = 0; column < this.grid[row].length; column++) {
          this.ctx.beginPath();
          if (this.grid[row][column] === 1) {
            this.ctx.fillStyle = "black";
          } else {
            this.ctx.fillStyle = "white";
          }
          // this.ctx.fillRect(
          //   row * this.cellSize,
          //   column * this.cellSize,
          //   this.cellSize,
          //   this.cellSize
          // );
          // this.ctx.stroke();
          this.ctx.arc(
            row * this.cellSize + this.cellSize / 2,
            column * this.cellSize + this.cellSize / 2,
            this.cellSize / 4,
            0,
            2 * Math.PI
          );
          this.ctx.fill();
        }
      }
    },
    checkNeighbors(totalNeighbors) {
      if (totalNeighbors == 2 || totalNeighbors == 3) {
        return 1;
      } else {
        return 0;
      }
    },
    cellSurvives(row, column, totalRows, totalColumns) {
      if (row == 0 && column == 0) {
        const totalNeighbors =
          this.grid[row][column + 1] +
          this.grid[row + 1][column] +
          this.grid[row + 1][column + 1];
        return this.checkNeighbors(totalNeighbors);
      } else if (row == totalRows && column == 0) {
        const totalNeighbors =
          this.grid[row - 1][column] +
          this.grid[row - 1][column + 1] +
          this.grid[row][column + 1];
        return this.checkNeighbors(totalNeighbors);
      } else if (row == 0 && column == totalColumns) {
        const totalNeighbors =
          this.grid[row][column - 1] +
          this.grid[row + 1][column - 1] +
          this.grid[row + 1][column];
        return this.checkNeighbors(totalNeighbors);
      } else if (row == totalRows && column == totalColumns) {
        const totalNeighbors =
          this.grid[row - 1][column - 1] +
          this.grid[row - 1][column] +
          this.grid[row][column - 1];
        return this.checkNeighbors(totalNeighbors);
      } else if (row == totalRows) {
        const totalNeighbors =
          this.grid[row - 1][column - 1] +
          this.grid[row - 1][column] +
          this.grid[row - 1][column + 1] +
          this.grid[row][column - 1] +
          this.grid[row][column + 1];
        return this.checkNeighbors(totalNeighbors);
      } else if (row == 0) {
        const totalNeighbors =
          this.grid[row][column - 1] +
          this.grid[row + 1][column - 1] +
          this.grid[row + 1][column] +
          this.grid[row + 1][column + 1] +
          this.grid[row][column + 1];
        return this.checkNeighbors(totalNeighbors);
      } else if (column == totalColumns) {
        const totalNeighbors =
          this.grid[row - 1][column - 1] +
          this.grid[row - 1][column] +
          this.grid[row][column - 1] +
          this.grid[row + 1][column - 1] +
          this.grid[row + 1][column];
        return this.checkNeighbors(totalNeighbors);
      } else if (column == 0) {
        const totalNeighbors =
          this.grid[row - 1][column] +
          this.grid[row - 1][column + 1] +
          this.grid[row][column + 1] +
          this.grid[row + 1][column] +
          this.grid[row + 1][column + 1];
        return this.checkNeighbors(totalNeighbors);
      } else {
        if (row == 0 || column == 0) {
          console.log(row, column);
        }
        const totalNeighbors =
          this.grid[row - 1][column - 1] +
          this.grid[row - 1][column] +
          this.grid[row - 1][column + 1] +
          this.grid[row][column - 1] +
          this.grid[row][column + 1] +
          this.grid[row + 1][column - 1] +
          this.grid[row + 1][column] +
          this.grid[row + 1][column + 1];
        return this.checkNeighbors(totalNeighbors);
      }
    },
    async update() {
      const newGrid = JSON.parse(JSON.stringify(this.grid));
      const totalRows = this.grid.length;
      const totalColumns = this.grid[0].length;

      for (let row = 0; row < totalRows; row++) {
        for (let column = 0; column < totalColumns; column++) {
          newGrid[row][column] = this.cellSurvives(
            row,
            column,
            totalRows - 1,
            totalColumns - 1
          );
        }
      }

      this.grid = JSON.parse(JSON.stringify(newGrid));

      return;
    },
    init() {
      this.canvas = this.$refs.canvas;
      this.ctx = this.canvas.getContext("2d");

      for (let i = 0; i < this.width / this.cellSize; i++) {
        this.grid[i] = [];
        for (let j = 0; j < this.height / this.cellSize; j++) {
          this.grid[i][j] = Math.floor(Math.random() * 2);
        }
      }
      this.draw();
    },
  },
  async mounted() {
    this.init();

    setInterval(async () => {
      await this.update();

      this.draw();
    }, 250);
  },
};
</script>

<style scoped>
a {
  color: #42b983;
}

label {
  margin: 0 0.5em;
  font-weight: bold;
}

code {
  background-color: #eee;
  padding: 2px 4px;
  border-radius: 4px;
  color: #304455;
}
</style>
