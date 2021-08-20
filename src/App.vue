<template>
  <div id="app">
    <div class="squares">
      <h1 class="msg">{{ msg }}</h1>
      <div
        class="square-item"
        :class="{ active: isActive == square.name, unclicked: isClicked }"
        v-for="square in squares"
        :key="square.id"
        :style="{ background: square.name }"
        @click="playerOrder(square.name)"
      ></div>
    </div>
    <div class="round">Уровень: {{ getlLevelRound }}</div>
    <div class="controls">
      <div>
        <button @click="startGame" class="btn btn-start">START</button>
        <button @click="resetGame" class="btn btn-reset">RESET</button>
      </div>
      <div>
        <div>
          <input
            name="complexity"
            type="radio"
            id="easy"
            value="easy"
            v-model="complexity"
          />
          <label for="easy">Легкий</label>

          <input
            name="complexity"
            type="radio"
            id="normal"
            value="normal"
            v-model="complexity"
          />
          <label for="normal">Средний</label>

          <input
            name="complexity"
            type="radio"
            id="hard"
            value="hard"
            v-model="complexity"
          />
          <label for="hard">Сложный</label>
        </div>
      </div>
    </div>
    <div class="sounds hidden">
      <audio
        v-for="sound in sounds"
        :key="sound.id"
        :ref="sound.name"
        :src="sound.src"
      ></audio>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      squares: [
        {
          id: 1,
          name: "blue",
        },
        {
          id: 2,
          name: "red",
        },
        {
          id: 3,
          name: "green",
        },
        {
          id: 4,
          name: "yellow",
        },
      ],
      sounds: [
        {
          id: 1,
          name: "blue",
          src: "./assets/sound/sounds1.mp3",
        },
        {
          id: 2,
          name: "red",
          src: "./assets/sound/sounds2.mp3",
        },
        {
          id: 3,
          name: "green",
          src: "./assets/sound/sounds3.mp3",
        },
        {
          id: 4,
          name: "yellow",
          src: "./assets/sound/sounds4.mp3",
        },
      ],
      order: [],
      orderPlayer: [],
      level: 0,
      targetLevel: 10,
      isClicked: true,
      msg: "",
      isActive: "",
      complexity: "easy",
      speed: 1500,
    };
  },
  computed: {
    getlLevelRound() {
      return this.level;
    },
  },
  watch: {
    complexity() {
      if (this.complexity == "easy") {
        this.speed = 1500;
      } else if (this.complexity == "normal") {
        this.speed = 1000;
      } else {
        this.speed = 400;
      }
      this.resetGame();
      return;
    },
  },
  methods: {
    startGame() {
      this.nextRound();
    },
    nextRound() {
      this.level += 1;
      this.isClicked = true;
      this.msg = "";

      let nextOrder = [...this.order];
      nextOrder.push(this.randomOrder());
      this.playRound(nextOrder);
      this.order = [...nextOrder];

      setTimeout(() => {
        this.isClicked = false;
      }, this.level * this.speed);
    },
    randomOrder() {
      let rnd = Math.floor(Math.random() * this.squares.length);
      return this.squares[rnd].name;
    },
    playRound(nextOrder) {
      nextOrder.forEach((color, index) => {
        setTimeout(() => {
          this.isActiveTiles(color, index);
        }, (index + 1) * this.speed);
      });
    },
    isActiveSound(sound, color) {
      if (sound) {
        this.sounds.forEach((item) => {
          if (item.name == color) {
            let audio = new Audio(require(`${item.src}`));
            audio.play();
          }
        });
      }
    },
    isActiveTiles(color) {
      const sound = this.$refs[color];
      this.isActive = color;
      this.isActiveSound(sound, color);
      setTimeout(() => {
        this.isActive = "";
      }, 300);
    },
    playerOrder(tile) {
      const sound = this.$refs[tile];
      this.orderPlayer.push(tile);
      this.isActive = tile;
      this.isActiveSound(sound, tile);

      setTimeout(() => {
        this.isActive = "";
      }, 200);

      for (let i = 0; i < this.orderPlayer.length; i++) {
        if (this.orderPlayer[i] !== this.order[i]) {
          this.msg = "Упс, ошибка! Попробуйте еще раз!";
          this.resetGame();
          return;
        }
      }

      if (this.orderPlayer.length === this.order.length) {
        this.orderPlayer = [];
        if (this.level === this.targetLevel) {
          this.msg = "Поздравляю с победой! Сыграйте еще раз!";
          this.resetGame();
        } else {
          setTimeout(() => {
            this.nextRound();
          }, 200);
        }
      }
    },
    resetGame() {
      this.level = 0;
      this.order = [];
      this.orderPlayer = [];
      this.isClicked = true;
    },
  },
};
</script>

<style>
#app {
  width: 400px;
  margin: 0 auto;
}

.squares {
  width: 400px;
  margin: 100px auto 0px;
  display: flex;
  flex-wrap: wrap;
  align-items: flex-start;
  justify-content: flex-start;
}

.square-item {
  width: 200px;
  height: 200px;
  opacity: 0.6;
  cursor: pointer;
}

.square-item.active {
  opacity: 1;
}

.controls {
  margin: 20px 0px 0px 0px;
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.btn-reset {
  margin: 0px 0px 0px 8px;
}

.hidden {
  display: none;
}

.unclicked {
  pointer-events: none;
}

.round {
  margin: 15px 0px 15px 0px;
  font-size: 18px;
}

.msg {
  font-size: 22px;
}
</style>
