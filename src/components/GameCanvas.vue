<template>
  <div>
    <canvas ref="canvas" width="400" height="400" class="game-canvas"></canvas>
    <div class="score">Pontuação: {{ score }}</div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      pacmanPos: { x: 200, y: 200 },
      pacmanSize: 40,
      pacmanSpeed: 5,
      walls: [
        { x: 50, y: 50, width: 300, height: 10 },
        { x: 50, y: 50, width: 10, height: 300 },
        { x: 50, y: 340, width: 300, height: 10 },
        { x: 340, y: 50, width: 10, height: 300 },
        { x: 100, y: 100, width: 10, height: 100 },
        { x: 200, y: 100, width: 10, height: 100 },
        { x: 100, y: 200, width: 100, height: 10 }
      ],
      foods: [
        { x: 70, y: 70, width: 10, height: 10 },
        { x: 150, y: 70, width: 10, height: 10 },
        { x: 230, y: 70, width: 10, height: 10 },
        { x: 70, y: 150, width: 10, height: 10 },
        { x: 150, y: 150, width: 10, height: 10 },
        { x: 230, y: 150, width: 10, height: 10 }
      ],
      score: 0,
      ghosts: [
        { x: 100, y: 100, size: 30 },
        { x: 300, y: 300, size: 30 }
      ]
    };
  },
  mounted() {
    this.updateCanvas();
    window.addEventListener('keydown', this.handleKeyDown);
    this.ghostsInterval = setInterval(this.moveGhosts, 100);
  },
  beforeDestroy() {
    window.removeEventListener('keydown', this.handleKeyDown);
    clearInterval(this.ghostsInterval);
  },
  methods: {
    detectCollision(x, y) {
      return this.walls.some(wall =>
        x < wall.x + wall.width &&
        x + this.pacmanSize > wall.x &&
        y < wall.y + wall.height &&
        y + this.pacmanSize > wall.y
      );
    },
    checkFoodCollision(x, y) {
      this.foods = this.foods.filter((food, index) => {
        if (x < food.x + food.width &&
            x + this.pacmanSize > food.x &&
            y < food.y + food.height &&
            y + this.pacmanSize > food.y) {
          this.score += 10;
          return false;
        }
        return true;
      });
    },
    handleKeyDown(event) {
      let newPos = { ...this.pacmanPos };
      switch (event.key) {
        case 'ArrowUp':
          newPos.y -= this.pacmanSpeed;
          break;
        case 'ArrowDown':
          newPos.y += this.pacmanSpeed;
          break;
        case 'ArrowLeft':
          newPos.x -= this.pacmanSpeed;
          break;
        case 'ArrowRight':
          newPos.x += this.pacmanSpeed;
          break;
        default:
          break;
      }
      if (!this.detectCollision(newPos.x, newPos.y)) {
        this.pacmanPos = newPos;
        this.checkFoodCollision(newPos.x, newPos.y);
        this.updateCanvas();
      }
    },
    moveGhosts() {
      this.ghosts = this.ghosts.map(ghost => {
        let newX = ghost.x;
        let newY = ghost.y;
        if (this.pacmanPos.x < ghost.x) newX -= 2;
        if (this.pacmanPos.x > ghost.x) newX += 2;
        if (this.pacmanPos.y < ghost.y) newY -= 2;
        if (this.pacmanPos.y > ghost.y) newY += 2;

        return { ...ghost, x: newX, y: newY };
      });
      this.updateCanvas();
    },
    updateCanvas() {
      const canvas = this.$refs.canvas;
      const ctx = canvas.getContext('2d');

      ctx.clearRect(0, 0, canvas.width, canvas.height);

      this.walls.forEach(wall => {
        ctx.fillStyle = 'blue';
        ctx.fillRect(wall.x, wall.y, wall.width, wall.height);
      });

      this.foods.forEach(food => {
        ctx.fillStyle = 'orange';
        ctx.fillRect(food.x, food.y, food.width, food.height);
      });

      this.ghosts.forEach(ghost => {
        ctx.beginPath();
        ctx.arc(ghost.x + ghost.size / 2, ghost.y + ghost.size / 2, ghost.size / 2, 0, 2 * Math.PI);
        ctx.fillStyle = 'red';
        ctx.fill();
        ctx.closePath();
      });

      ctx.beginPath();
      ctx.arc(this.pacmanPos.x + this.pacmanSize / 2, this.pacmanPos.y + this.pacmanSize / 2, this.pacmanSize / 2, 0.2 * Math.PI, 1.8 * Math.PI);
      ctx.lineTo(this.pacmanPos.x + this.pacmanSize / 2, this.pacmanPos.y + this.pacmanSize / 2);
      ctx.closePath();
      ctx.fillStyle = 'yellow';
      ctx.fill();
    }
  }
};
</script>

<style scoped>
.game-canvas {
  border: 2px solid #fff;
  background-color: #000;
}

.score {
  color: #fff;
  font-size: 20px;
  margin-top: 10px;
}
</style>
