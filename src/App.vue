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
          <GameWheel ref="gameWheel" :players="players" @spin-result="handleSpinResult" />
          <div id="pointer"></div> <!-- Ajout du pointeur -->
        </div>
        <p class="mt-3">{{ resultMessage }}</p>
      </div>
      <div class="col-md-4 col-12">
        <PlayerList
          :players="players.map(player => player.name)"
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
      players: [], // Les joueurs avec leur poids
      resultMessage: "",
    };
  },
  methods: {
    addPlayer(playerName) {
      if (this.players.length >= 5) {
        alert("You can only add 5 players!");
        return;
      }
      if (!this.players.some(player => player.name === playerName)) {
        this.players.push({ name: playerName, weight: 1 });
        this.$refs.gameWheel.redrawWheel(); // Redessiner la roue
      }
    },
    deletePlayer(index) {
      this.players.splice(index, 1);
      this.$refs.gameWheel.redrawWheel(); // Redessiner la roue
    },
    handleSpinResult(playerName) {
      const player = this.players.find(p => p.name === playerName);
      if (player) {
        player.weight = 1; // Réinitialiser le poids du joueur sélectionné
      }
      this.players.forEach(p => {
        if (p.name !== playerName) {
          p.weight += 1; // Augmenter le poids des autres joueurs
        }
      });
      this.resultMessage = `Player: ${playerName}`;
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
  right: -20px;
  transform: translateY(-50%) rotate(180deg);
  width: 0;
  height: 0;
  border-style: solid;
  border-width: 10px 0 10px 20px;
  border-color: transparent transparent transparent #fff;
  z-index: 10;
}
</style>
