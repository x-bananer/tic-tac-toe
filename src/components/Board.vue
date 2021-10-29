<template>

  <div class="row">
  <div class="col-md-3 mb-3  text-center">
    <div class="history">
      <h2 class="mt-3 text-uppercase">История побед</h2>
      <div v-for="(game, idx) in history" :key="idx">
        Игра {{ idx + 1 }}: <span class="sign">{{ game }}</span> победил
      </div>
    </div>
  </div>
  <div class="col-md-6 mx-sm-3 text-center">
    <h2 class="text-uppercase my-sm-3" v-if="winner">Победил: <span class="sign">{{ winner }}</span></h2>
    <h2 class="text-uppercase my-sm-3" v-else>Ходит: <span class="sign">{{ player }}</span></h2>

    <div v-for="(_, x) in 3" :key="x" class="row justify-content-center">
      <button v-for="(_, y) in 3" :key="y" class="square" @click="move(x, y)">
        {{ squares[x][y] }}
      </button>
    </div>

    <button @click="reset" class="btn mt-3 mb-3 text-uppercase game-btn">Новая игра</button>
  </div>
  </div>
</template>

<script>
import { computed, onMounted, ref, watch } from "vue";

const calculateWinner = (squares) => {
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6],
  ];
  for (let i = 0; i < lines.length; i++) {
    const [a, b, c] = lines[i];
    if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
      return squares[a];
    }
  }
  return null;
};

export default {
  setup() {
    const player = ref("X");
    const squares = ref([
      ["", "", ""],
      ["", "", ""],
      ["", "", ""],
    ]);
    const winner = computed(() => calculateWinner(squares.value.flat()));

    const move = (x, y) => {
      if (winner.value) return;
      squares.value[x][y] = player.value;
      player.value = player.value === "O" ? "X" : "O";
    };

    const reset = () => {
      player.value = "X";
      squares.value = [
        ["", "", ""],
        ["", "", ""],
        ["", "", ""],
      ];
    };

    const history = ref([]);
    watch(winner, (current, previous) => {
      if (current && !previous) {
        history.value.push(current);
        localStorage.setItem("history", JSON.stringify(history.value));
      }
    });

    onMounted(() => {
      history.value = JSON.parse(localStorage.getItem("history")) ?? [];
    });

    return { winner, player, squares, move, reset, history };
  },
};
</script>

<style scoped>
.square {
  background: #bed3c3;
  border: 5px solid #fff;
  border-radius: 15px;
  font-size: 90px;
  color: #fff;
  font-weight: bold;
  line-height: 34px;
  height: 130px;
  margin-right: -1px;
  margin-top: -1px;
  padding: 0;
  text-align: center;
  width: 130px;
}

.sign {
  color: #4a919e;
}

.game-btn {
  background: #ebaca2;
  color: #fff;
}

.game-btn:hover {
  background: #ce6a6b;
  color: #fff;
}
</style>
