<template>
  <div class="wheel-container">
    <div class="svg">
      <img class="mosalas" src="../assets/images/wheel/mosalas.png" alt="" />
    </div>
    <canvas ref="wheelCanvas" width="500" height="500" class="canvas"></canvas>
    <img class="round" src="../assets/images/wheel/round.gif" alt="" />

    <button v-if="CenterBtn" class="button" @click="startSpin">
      {{ textButton }}
    </button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      angle: 0, // زاویه فعلی چرخ
      spinning: false,
    };
  },
  props: {
    prizes: Array,
    flagSpin: Boolean,
    prizesId: {
      type: Number,
    },
    textButton: {
      type: String,
      default: "Spin",
    },
    CenterBtn: {
      type: Boolean,
      default: true,
    },
  },
  watch: {
    flagSpin(newflagSpin) {
      console.log(newflagSpin, "flagSpin");
    },
  },
  mounted() {
    this.drawWheel();
  },
  computed: {
    allSpinRewards() {
      return this.prizes.map((reward, index) => ({
        id: reward.id,
        name: reward.name,
        value: reward.value,
        location: this.location(index),
      }));
    },
  },
  methods: {
    getRandomPrize() {
      let totalProbability = this.allSpinRewards.reduce(
        (sum, reward) => sum + reward.probability,
        0
      );
      let random = Math.random() * totalProbability;

      let cumulative = 0;
      for (let reward of this.allSpinRewards) {
        cumulative += reward.probability;
        if (random < cumulative) {
          return reward.id;
        }
      }
    },
    location(index) {
      return { startLo: index, endLo: index + 1 };
    },
    drawWheel(rotation = 0) {
      const canvas = this.$refs.wheelCanvas;
      const ctx = canvas.getContext("2d");
      const numSectors = this.prizes.length;
      const anglePerSector = (2 * Math.PI) / numSectors;
      const colors = ["#00afad", "#fff", "#f173ac"];

      ctx.clearRect(0, 0, canvas.width, canvas.height);
      ctx.save();
      ctx.translate(canvas.width / 2, canvas.height / 2);
      ctx.rotate((rotation * Math.PI) / 180);

      for (let i = 0; i < numSectors; i++) {
        ctx.beginPath();
        ctx.moveTo(0, 0);
        ctx.arc(
          0,
          0,
          200,
          anglePerSector * i - Math.PI / 2,
          anglePerSector * (i + 1) - Math.PI / 2
        );

        ctx.fillStyle = colors[i % colors.length];

        ctx.fill();
        //   ctx.stroke();
        ctx.shadowColor = "rgba(0, 0, 0, 0.5)"; // رنگ سایه
        ctx.shadowBlur = 14; // میزان تاری سایه
        ctx.shadowOffsetX = 0; // جابجایی سایه در محور X
        ctx.shadowOffsetY = 0; // جابجایی سایه در محور Y

        // Draw text
        ctx.save();
        ctx.fillStyle = "#000"; // Set stroke style to black for the outline
        ctx.font = "bold 16px Arial";
        ctx.textAlign = "center";
        ctx.textBaseline = "middle";

        ctx.translate(
          Math.cos(anglePerSector * (i + 0.5) - Math.PI / 2) * 120,
          Math.sin(anglePerSector * (i + 0.5) - Math.PI / 2) * 120
        );
        ctx.rotate(anglePerSector * (i + 0.5) - Math.PI / 2);

        ctx.strokeStyle = "#000";
        ctx.lineWidth = 3;
        ctx.strokeText(this.prizes[i].name, 0, 0);
        ctx.fillStyle = "#fff";
        ctx.fillText(this.prizes[i].name, 0, 0);

        ctx.restore();
      }

      ctx.restore();
    },
    startSpin() {
      this.$emit("onCanvas");
    },
    spinWheel(prizesId) {
      console.log(this.prizeId, "dfghjkl;sdfghjkl;sdfghjklsdfghjk");
      let winningPrizIndexe;
      // if (this.prizeId !== null) {
      //   winningPrizIndexe = this.allSpinRewards.find((item) => item.id == prizesId);
      // } else {
      //   winningPrizIndexe = this.getRandomPrize();
      // }

      console.log(this.allSpinRewards, "this.prizes this.prizess");
      const numSectors = this.allSpinRewards.length;

      winningPrizIndexe = this.allSpinRewards.find(
        (item) => item.id == prizesId
      );
      console.log(winningPrizIndexe);

      let min =
        (numSectors - winningPrizIndexe.location.startLo) * (360 / numSectors);
      let max =
        (numSectors - winningPrizIndexe.location.endLo) * (360 / numSectors);
      let targetRotation = 360 * 5 + (min + max) / 2; // اضافه کردن چرخش‌های قبلی
      console.log(targetRotation, "targetRotation");

      this.animateSpin(targetRotation);
      targetRotation = 0;
      //اینجا میخوام تابعی در کامپونتت پدر رو فراخوانی کنم
      setTimeout(() => {
        this.$emit("onRotateEnd");
      }, 3000);
    },
    animateSpin(targetRotation) {
      const duration = 3000;
      const start = performance.now();
      const initialAngle = 0;
      const animate = (time) => {
        let elapsed = time - start;
        let progress = Math.min(elapsed / duration, 1);
        this.angle =
          initialAngle +
          (targetRotation - initialAngle) * easeOutCubic(progress);

        this.drawWheel(this.angle);

        if (progress < 1) {
          requestAnimationFrame(animate);
        }
        //   else {
        //     this.spinning = false;
        //   }
      };

      requestAnimationFrame(animate);
      this.angle = 0;
    },
  },
};

function easeOutCubic(t) {
  return 1 - Math.pow(1 - t, 3);
}
</script>

<style>
.wheel-container {
  position: relative;
  width: fit-content;
  display: flex;
  justify-content: center;
}

.button {
  width: 70px;
  height: 70px;
  border-radius: 50%;
  background: rgb(121, 76, 51);
  background: radial-gradient(
    circle,
    rgba(121, 76, 51, 1) 0%,
    rgba(125, 92, 83, 1) 53%,
    rgba(72, 47, 36, 1) 100%
  );
  position: absolute;
  top: 50%;
  left: 50%;
  color: #fff;
  font-size: 22px;
  font-weight: 700;
  transform: translate(-50%, -50%);
}

.round {
  width: 623px;
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-49%, -52%);
}
.svg {
  position: absolute;
  top: -7%;
  left: 50%;
  z-index: 10;
  transform: translate(-50%, 50%);
  width: 162px;
  display: flex;
  justify-content: center;
}
.mosalas {
  width: 50px;
}

.canvas {
  width: 500px;
  height: 500px;
}

@media screen and (max-width: 768px) {
  .canvas {
    width: 90%;
    height: 90%;
    margin: 0 auto !important;
  }
  .round {
    width: 442px;
    position: absolute;
    left: 54%;
    top: 50%;
    transform: translate(-53%, -52%);
  }
  .svg {
    position: absolute;
    top: -12%;
    left: 48%;
    z-index: 10;
    transform: translate(-50%, 50%);
    width: 69px;
  }
  .mosalas {
    width: 50px;
  }
}
</style>
