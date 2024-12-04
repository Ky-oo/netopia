<template>
<div class="wheel-container position-relative">
  <canvas ref="wheelCanvas" width="400" height="400"></canvas>
  <!-- Bouton repositionné ici -->
  <button
    class="btn btn-primary rounded mt-3"
    @click="spinWheel"
    :disabled="spinning"
  >
    Spin the Wheel
  </button>
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
    };
  },
  computed: {
    numOptions() {
      return this.players.length;
    },
    anglePerOption() {
      return (2 * Math.PI) / this.numOptions;
    },
  },
  methods: {
    drawWheel() {
      const canvas = this.$refs.wheelCanvas;
      const ctx = canvas.getContext("2d");
      const radius = canvas.width / 2;
      const colors = ["#e94695", "#49b671", "#e03a27", "#f6bf43", "#14a0fa"];

      ctx.clearRect(0, 0, canvas.width, canvas.height);

      this.players.forEach((player, i) => {
        const startAngle = i * this.anglePerOption;
        const endAngle = startAngle + this.anglePerOption;

        ctx.beginPath();
        ctx.moveTo(radius, radius);
        ctx.arc(radius, radius, radius, startAngle, endAngle);
        ctx.closePath();
        ctx.fillStyle = colors[i % colors.length];
        ctx.fill();

        ctx.save();
        ctx.translate(radius, radius);
        ctx.rotate(startAngle + this.anglePerOption / 2);
        ctx.textAlign = "right";
        ctx.fillStyle = "white";
        ctx.font = "bold 14px Arial";
        ctx.fillText(player, radius - 10, 5);
        ctx.restore();
      });
    },
    spinWheel() {
      if (this.spinning) return;
      this.spinning = true;

      const spinAngle = Math.random() * 360 + 1440; // Random spin
      const duration = 3000; // Spin duration

      let startTime = null;
      const finalAngle = this.currentAngle + (spinAngle * Math.PI) / 180;

      const animate = (timestamp) => {
        if (!startTime) startTime = timestamp;
        const progress = timestamp - startTime;

        const easing = this.easeOutCubic(progress / duration);
        const angle =
          this.currentAngle + easing * (finalAngle - this.currentAngle);

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
          const selectedIndex =
            Math.floor(
              (2 * Math.PI - this.currentAngle) / this.anglePerOption
            ) % this.numOptions;
          this.$emit("spin-result", this.players[selectedIndex]);
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