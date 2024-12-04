<template>
  <div id="app" class="container">
    <img
      src="./assets/neotpia_logo.png"
      alt="Netopia"
      class="img-fluid my-3"
    />
    <div class="row">
      <div class="col-md-8 col-12 d-flex flex-column align-items-center position-relative">
        <div class="wheel-container position-relative">
          <GameWheel ref="gameWheel" :players="players" @spin-result="handleSpinResult" @redraw-wheel="redrawWheel" />
          <div id="pointer"></div> <!-- Ajout du pointeur -->
        </div>
        <p class="mt-3">{{ resultMessage }}</p>
        <p class="mt-3">Step forward: {{ spinCount }}</p>
      </div>
      <div class="col-md-4 col-12">
        <PlayerList
          :players="players"
          @add-player="addPlayer"
          @delete-player="deletePlayer"
        />
      </div>
    </div>
  </div>
</template>

<script>
import GameWheel from "./components/Wheel.vue";
import PlayerList from "./components/PlayerList.vue";
import 'bootstrap/dist/css/bootstrap.css';

export default {
  components: {
    GameWheel,
    PlayerList,
  },
  data() {
    return {
      players: ["Add a player"], // Default state
      resultMessage: "",
      spinCount: 0, // Compteur de tours
    };
  },
  methods: {
    addPlayer(playerName) {
      if (this.players.length >= 5) {
        alert("You can only add 5 players!");
        return;
      }
      if (!this.players.includes(playerName)) {
        if (this.players.length === 1 && this.players[0] === "Add a player") {
          this.players = [];
        }
        this.players.push(playerName);
        this.$refs.gameWheel.redrawWheel(); // Redessiner la roue
      }
    },
    deletePlayer(index) {
      this.players.splice(index, 1);
      if (this.players.length === 0) {
        this.players = ["Add a player"];
      }
      this.$refs.gameWheel.redrawWheel(); // Redessiner la roue
    },
    handleSpinResult(player) {
      this.resultMessage = `Player: ${player}`;
      this.spinCount++; // Incrémenter le compteur de tours
    },
    redrawWheel() {
      this.$refs.gameWheel.drawWheel();
    },
  },
};
</script>

<style>
.container {
  text-align: center;
}

.wheel-container {
  position: relative;
  width: 100%;
  max-width: 400px;
  margin: auto;
}

#pointer {
  position: absolute;
  top: 50%;
  right: -20px; /* Juste à droite de la roue */
  transform: translateY(-50%) rotate(180deg); /* Rotation de 180° */
  width: 0;
  height: 0;
  border-style: solid;
  border-width: 10px 0 10px 20px; /* Triangle vers la gauche */
  border-color: transparent transparent transparent #fff;
  z-index: 10;
}
</style>