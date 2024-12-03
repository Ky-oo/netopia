<template>
  <div>
    <form @submit.prevent="submitPlayer">
      <div class="form-group">
        <label for="player-name">Player Name:</label>
        <input
          type="text"
          id="player-name"
          v-model="playerName"
          class="form-control"
          required
        />
      </div>
      <button type="submit" class="btn btn-primary">Add Player</button>
    </form>
    <h2 class="mt-3">Players</h2>
    <ul class="list-group">
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
    </ul>
  </div>
</template>

<script>
export default {
  props: {
    players: {
      type: Array,
      required: true,
    },
  },
  data() {
    return {
      playerName: "",
    };
  },
  methods: {
    submitPlayer() {
      if (this.playerName) {
        this.$emit("add-player", this.playerName);
        this.playerName = "";
      }
    },
    deletePlayer(index) {
      this.$emit("delete-player", index);
    },
  },
};
</script>
