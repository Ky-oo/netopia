<template>
  <div id="app" class="container">
    <img
      src="./assets/neotpia_logo.png"
      alt="Netopia"
      width="40%"
      height="40%"
    />
    <div class="row">
      <div class="col-md-8 col-12 d-flex flex-column align-items-center">
        <Wheel :players="players" @spin-result="handleSpinResult" />
        <p class="mt-3">{{ resultMessage }}</p>
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
import Wheel from "./components/Wheel.vue";
import PlayerList from "./components/PlayerList.vue";
import 'bootstrap/dist/css/bootstrap.css';


export default {
  components: {
    Wheel,
    PlayerList,
  },
  data() {
    return {
      players: ["Add a player"], // Default state
      resultMessage: "",
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
      }
    },
    deletePlayer(index) {
      this.players.splice(index, 1);
      if (this.players.length === 0) {
        this.players = ["Add a player"];
      }
    },
    handleSpinResult(player) {
      this.resultMessage = `Player: ${player}`;
    },
  },
};
</script>

<style>
.container {
  text-align: center;
}
</style>
