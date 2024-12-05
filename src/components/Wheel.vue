<template>
  <div class="wheel-container position-relative">
    <canvas ref="wheelCanvas" width="400" height="400"></canvas>
    <button
      class="btn btn-primary rounded mt-3"
      @click="spinWheel"
      :disabled="spinning"
    >
      Spin the Wheel
    </button>
    <p class="mt-3"> Step Forward: {{ spinCount }}</p>
  </div>
</template>

<script>
export default {
  name: "GameWheel",
  props: {
    players: {
      type: Array,
      required: true,
    },
  },
  data() {
    return {
      spinning: false,
      currentAngle: 0,
      localPlayers: [],
      spinCount: 0
    };
  },
  computed: {
    totalWeight() {
      return this.localPlayers.reduce((sum, player) => sum + player.weight, 0);
    },
  },
  watch: {
    players: {
      handler(newPlayers) {
        this.localPlayers = newPlayers.length ? newPlayers : [{ name: 'Add a player', weight: 1 }];
      },
      immediate: true,
    },
  },
  methods: {
    drawWheel() {
      const canvas = this.$refs.wheelCanvas;
      const ctx = canvas.getContext("2d");
      const radius = canvas.width / 2;
      const colors = ["#e94695", "#49b671", "#e03a27", "#f6bf43", "#14a0fa"];

      ctx.clearRect(0, 0, canvas.width, canvas.height);

      let startAngle = 0;
      this.localPlayers = this.players

      if(this.localPlayers.length === 0){
        this.localPlayers = [{name: '', weight: 1}, {name: '', weight: 1}, {name: '', weight: 1}, {name: '', weight: 1}, {name: '', weight: 1}]
      }

      this.localPlayers.forEach((player, i) => {
        const sliceAngle = (player.weight / this.totalWeight) * (2 * Math.PI);
        const endAngle = startAngle + sliceAngle;

        ctx.beginPath();
        ctx.moveTo(radius, radius);
        ctx.arc(radius, radius, radius, startAngle, endAngle);
        ctx.closePath();
        ctx.fillStyle = colors[i % colors.length];

        ctx.fill();

        ctx.save();
        ctx.translate(radius, radius);
        ctx.rotate(startAngle + sliceAngle / 2);
        ctx.textAlign = "right";
        ctx.fillStyle = "white";
        ctx.font = "bold 14px Arial";
        ctx.fillText(player.name, radius - 10, 5);
        ctx.restore();

        startAngle = endAngle;
      });
    },
    spinWheel() {
      if (this.spinning) return;
      this.spinning = true;
      this.localPlayers = this.players
      this.spinCount === 8 ? this.spinCount = 1 : this.spinCount++
      const spinAngle = Math.random() * 360 + 1440; // Random spin
      const duration = 3000; // Spin duration

      let startTime = null;
      const finalAngle = this.currentAngle + (spinAngle * Math.PI) / 180;

      const animate = (timestamp) => {
        if (!startTime) startTime = timestamp;
        const progress = timestamp - startTime;

        const easing = this.easeOutCubic(progress / duration);
        const angle = this.currentAngle + easing * (finalAngle - this.currentAngle);

        const canvas = this.$refs.wheelCanvas;
        const ctx = canvas.getContext("2d");
        const radius = canvas.width / 2;

        ctx.save();
        ctx.translate(radius, radius);
        ctx.rotate(angle);
        ctx.translate(-radius, -radius);
        this.drawWheel();
        ctx.restore();

        if (progress < duration) {
          requestAnimationFrame(animate);
        } else {
          this.spinning = false;
          this.currentAngle = finalAngle % (2 * Math.PI);

          // Determine selected player
          let currentAngle = 0;
          for (const player of this.localPlayers) {
            const sliceAngle = (player.weight / this.totalWeight) * (2 * Math.PI);
            if (this.currentAngle >= currentAngle && this.currentAngle < currentAngle + sliceAngle && player.name !== '') {
              this.$emit("spin-result", player.name);
              break;
            }
            currentAngle += sliceAngle;
          }
        }
      };

      requestAnimationFrame(animate);
    },
    easeOutCubic(t) {
      return 1 - Math.pow(1 - t, 3);
    },
    redrawWheel() {
      this.drawWheel();
    },
  },
  mounted() {
    this.drawWheel();
  },
};
</script>

<style scoped>
.wheel-container {
  position: relative;
  width: 100%;
  max-width: 400px;
  margin: auto;
}
</style>