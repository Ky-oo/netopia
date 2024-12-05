<template>
  <div>
    <form @submit.prevent="submitPlayer">
      <div class="form-group">
        <label for="player-name">Player Name:</label>
        <input
          type="text"
          id="player-name"
          v-model="playerName"
          class="form-control mt-2"
          required
        />
      </div>
      <button type="submit" class="btn btn-primary mt-2">Add Player</button>
    </form>
    <h2 class="mt-3">Players</h2>
    <ul class="list-group">
      <template v-if="players.length === 1 && players[0] === 'Add a player'">
        <li class="list-group-item">No players added yet</li>
      </template>
      <template v-else>
        <li
          v-for="(player, index) in players"
          :key="index"
          class="list-group-item d-flex justify-content-between align-items-center"
        >
          {{ player }}
          <button @click="deletePlayer(index)" class="btn btn-danger btn-sm">
            Delete
          </button>
        </li>
      </template>
    </ul>
  </div>
</template>

<script>
export default {
  props: {
    players: {
      type: Array,
      required: true,
      default: () => [],
    },
  },
  data() {
    return {
      playerName: "",
    };
  },
  methods: {
    submitPlayer() {
      if (this.playerName.trim() !== "") {
        this.$emit("add-player", this.playerName.trim());
        this.playerName = "";
        this.drawWheel
      }
    },
    deletePlayer(index) {
      this.$emit("delete-player", index);
    },
  },
};
</script>

<style scoped>
.form-group {
  margin-bottom: 1rem;
}
</style>