<template>
  <div class="header">
    <button class="btn" @click.prevent="reset">Reset</button>
    <p class="score">Score: {{ score }}</p>
  </div>
  <div class="canvas" ref="canvas" @click.prevent="handleIncorrectClick">
    <template v-for="ball in balls" :key="ball.ballId">
      <div
        class="ball"
        :id="ball.ballId"
        :style="{ top: ball.top + 'px', left: ball.left + 'px' }"
        @click.prevent="handleClick(ball.ballId)"
      ></div>
    </template>
  </div>
</template>

<style scoped>
.header {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 3rem;
  margin: 5px;
}
.btn {
  border: none;
  width: 100px;
  height: 50px;
  border-radius: 6px;
  font-size: 18px;
  font-weight: 600;
}
.score {
  font-size: 18px;
  font-weight: 600;
}
.canvas {
  border: 1px solid;
  height: 88vh;
  width: 85svw;
  position: relative;
  margin: auto;
}
.ball {
  width: 70px;
  height: 70px;
  border-radius: 100%;
  border: none;
  background-color: rgb(233, 35, 35);
  position: absolute;
  cursor: pointer;
}
</style>

<script>
export default {
  data() {
    return {
      balls: [
        { ballId: crypto.randomUUID(), top: null, left: null },
        { ballId: crypto.randomUUID(), top: null, left: null },
        { ballId: crypto.randomUUID(), top: null, left: null },
        { ballId: crypto.randomUUID(), top: null, left: null },
        { ballId: crypto.randomUUID(), top: null, left: null },
        { ballId: crypto.randomUUID(), top: null, left: null },
      ],
      canvasWidth: null,
      canvasHeight: null,
      score: 0,
    };
  },
  mounted() {
    this.canvasWidth = this.$refs.canvas.clientWidth - 100;
    this.canvasHeight = this.$refs.canvas.clientHeight - 100;
    this.calculateBallPositions();
  },
  methods: {
    handleClick(id) {
      this.score++;
      this.balls = this.balls.filter((ball) => ball.ballId !== id);

      const newBall = {
        ballId: crypto.randomUUID(),
        top: Math.floor(Math.random() * this.canvasHeight),
        left: Math.floor(Math.random() * this.canvasWidth),
      };
      this.balls.push(newBall);
      this.verifyBallPositions(this.balls, newBall);
    },
    handleIncorrectClick() {
      if (!event.target.classList.contains("ball")) {
        this.score--;
        event.stopPropagation();
      }
    },
    verifyBallPositions(balls, ball) {
      let element = document.getElementById(ball.ballId);

      balls.forEach((otherBall) => {
        if (ball.ballId === otherBall.ballId) return;
        const distanceSquared =
          Math.pow(ball.top - otherBall.top, 2) + Math.pow(ball.left - otherBall.left, 2);
        if (distanceSquared <= Math.pow(this.threshold, 2)) {
          ball.top = Math.floor(Math.random() * this.canvasHeight);
          ball.left = Math.floor(Math.random() * this.canvasWidth);
        }
      });

      return ball;
    },
    calculateBallPositions() {
      this.balls = this.balls.map((ball) => {
        ball.top = Math.floor(Math.random() * this.canvasHeight);
        ball.left = Math.floor(Math.random() * this.canvasWidth);

        return ball;
      });
      this.balls = this.balls.map((ball) => {
        ball = this.verifyBallPositions(this.balls, ball);
        return ball;
      });
    },
    reset() {
      this.balls = [];
      this.score = 0;
      const newBalls = [];
      for (let i = 0; i < 6; i++) {
        let ball = {
          ballId: crypto.randomUUID(),
          top: Math.floor(Math.random() * this.canvasHeight),
          left: Math.floor(Math.random() * this.canvasWidth),
        };
        newBalls.push(ball);
        this.verifyBallPositions(newBalls, ball);
      }
      this.balls = [...newBalls];
    },
  },
  computed: {
    threshold() {
      return 200;
    },
  },
};
</script>
