<template>
  <div class="wheel-container">
    <canvas ref="wheelCanvas" width="500" height="500" class="canvas"></canvas>
    <img
      v-if="imgBorder"
      class="round imgBorder"
      src="../assets/images/wheel/round1.gif"
      alt=""
    />
    <audio ref="sound" src="../assets/images/wheel/wheel.mp3"></audio>
    <button v-if="CenterBtn" class="button" @click="startSpin">
      {{ textButton }}
    </button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      angle: 0,
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
    soundStatus: {
      type: Boolean,
      default: true,
    },
    imgBorder: {
      type: Boolean,
      default: true,
    },
    happyRain: {
      type :Boolean,
      default :true
    },
    styleWheel: Object,
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
        probability: reward.probability,
      }));
    },
  },
  methods: {
    getRandomPrize() {
      let totalProbability = this.allSpinRewards;
      let RandomArray = [];

      for (let i = 0; i < totalProbability.length; i++) {
        if (totalProbability[i].probability > 0) {
          for (let j = 0; j < totalProbability[i].probability; j++) {
            RandomArray.push(totalProbability[i]);
          }
        }
      }

      if (RandomArray.length > 0) {
        const randomIndex = Math.floor(Math.random() * RandomArray.length);

        return RandomArray[randomIndex];
      } else {
        return null;
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

        if(this.prizes[i].bgColor){
          ctx.fillStyle = this.prizes[i].bgColor
        }else{
           ctx.fillStyle = colors[i % colors.length];
        }
        ctx.fill();
        if (this.styleWheel.shadow) {
          ctx.shadowColor =
            this.styleWheel.shadow.shadowColor ?? "rgba(0, 0, 0, 0.5)";
          ctx.shadowBlur = this.styleWheel.shadow.shadowBlur ?? 14;
          ctx.shadowOffsetX = this.styleWheel.shadow.shadowOffsetX ?? 0;
          ctx.shadowOffsetY = this.styleWheel.shadow.shadowOffsetY ?? 0;
        }
        if (this.styleWheel.stroke) {
          ctx.stroke();
        }
        ctx.save(); 
        ctx.font =this.styleWheel.font ?? "bold 16px Arial";
        ctx.textAlign =this.styleWheel.textAlign ?? "center"; 
        ctx.textBaseline =this.styleWheel.textBaseline ?? "middle"; 

       
        ctx.translate(
          Math.cos(anglePerSector * (i + 0.5) - Math.PI / 2) * 120,
          Math.sin(anglePerSector * (i + 0.5) - Math.PI / 2) * 120
        );

       
        if (this.styleWheel.textOrientation === "vertical") {
          ctx.rotate(anglePerSector * (i + 0.5) - Math.PI / 2);
        } else if (this.styleWheel.textOrientation === "horizontal") {
          ctx.rotate(anglePerSector * (i + 0.5) - Math.PI);
        }

        if (this.styleWheel.twoLineText.status) {
          ctx.strokeStyle = this.styleWheel.twoLineText.outlineColor ?? "#000"; 
          ctx.lineWidth = this.styleWheel.twoLineText.lineWidth ?? 3; 
          ctx.strokeText(this.prizes[i].name, 0, 0); 
          ctx.fillStyle = this.styleWheel.twoLineText.inlineColor ?? "#fff"; 
          ctx.fillText(this.prizes[i].name, 0, 0);
        }else{
          ctx.fillStyle = this.styleWheel.colorText ?? "black"; 
          ctx.fillText(this.prizes[i].name, 0, 0); 
        }

        ctx.restore();
      }

      ctx.restore();
    },
    startSpin() {
      this.$emit("onCanvas");
    },
    spinWheel() {
      let winningPrizIndexe;

      console.log(this.allSpinRewards, "this.prizes this.prizess");
      const numSectors = this.allSpinRewards.length;
      this.getRandomPrize();

      if (this.prizesId) {
        winningPrizIndexe = this.allSpinRewards.find(
          (item) => item.id == this.prizesId
        );
        console.log(winningPrizIndexe);
      } else {
        winningPrizIndexe = this.getRandomPrize();
        this.$emit("prizesRandomIndex", winningPrizIndexe);
      }

      let min =
        (numSectors - winningPrizIndexe.location.startLo) * (360 / numSectors);
      let max =
        (numSectors - winningPrizIndexe.location.endLo) * (360 / numSectors);
      let targetRotation = 360 * 5 + (min + max) / 2; // اضافه کردن چرخش‌های قبلی
      console.log(targetRotation, "targetRotation");

      this.animateSpin(targetRotation);
      targetRotation = 0;

    
      if (this.soundStatus) {
        this.$refs.sound.play();
      }

      setTimeout(() => {
        this.winner()
      } , 5000)

      setTimeout(() => {
        this.$emit("onRotateEnd");
      }, 7000);
    },
    animateSpin(targetRotation) {
      const duration = 7000;
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
    winner() {
    if(this.happyRain){
      var confetti = {
        maxCount: 150,
        speed: 2,
        frameInterval: 15,
        alpha: 1,
        gradient: !1,
        start: null,
        stop: null,
        toggle: null,
        pause: null,
        resume: null,
        togglePause: null,
        remove: null,
        isPaused: null,
        isRunning: null,
      };
      !(function () {
        (confetti.start = s),
          (confetti.stop = w),
          (confetti.toggle = function () {
            e ? w() : s();
          }),
          (confetti.pause = u),
          (confetti.resume = m),
          (confetti.togglePause = function () {
            i ? m() : u();
          }),
          (confetti.isPaused = function () {
            return i;
          }),
          (confetti.remove = function () {
            stop(), (i = !1), (a = []);
          }),
          (confetti.isRunning = function () {
            return e;
          });
        var t =
            window.requestAnimationFrame ||
            window.webkitRequestAnimationFrame ||
            window.mozRequestAnimationFrame ||
            window.oRequestAnimationFrame ||
            window.msRequestAnimationFrame,
          n = [
            "rgba(30,144,255,",
            "rgba(107,142,35,",
            "rgba(255,215,0,",
            "rgba(255,192,203,",
            "rgba(106,90,205,",
            "rgba(173,216,230,",
            "rgba(238,130,238,",
            "rgba(152,251,152,",
            "rgba(70,130,180,",
            "rgba(244,164,96,",
            "rgba(210,105,30,",
            "rgba(220,20,60,",
          ],
          e = !1,
          i = !1,
          o = Date.now(),
          a = [],
          r = 0,
          l = null;
        function d(t, e, i) {
          return (
            (t.color =
              n[(Math.random() * n.length) | 0] + (confetti.alpha + ")")),
            (t.color2 =
              n[(Math.random() * n.length) | 0] + (confetti.alpha + ")")),
            (t.x = Math.random() * e),
            (t.y = Math.random() * i - i),
            (t.diameter = 10 * Math.random() + 5),
            (t.tilt = 10 * Math.random() - 10),
            (t.tiltAngleIncrement = 0.07 * Math.random() + 0.05),
            (t.tiltAngle = Math.random() * Math.PI),
            t
          );
        }
        function u() {
          i = !0;
        }
        function m() {
          (i = !1), c();
        }
        function c() {
          if (!i)
            if (0 === a.length)
              l.clearRect(0, 0, window.innerWidth, window.innerHeight), null;
            else {
              var n = Date.now(),
                u = n - o;
              (!t || u > confetti.frameInterval) &&
                (l.clearRect(0, 0, window.innerWidth, window.innerHeight),
                (function () {
                  var t,
                    n = window.innerWidth,
                    i = window.innerHeight;
                  r += 0.01;
                  for (var o = 0; o < a.length; o++)
                    (t = a[o]),
                      !e && t.y < -15
                        ? (t.y = i + 100)
                        : ((t.tiltAngle += t.tiltAngleIncrement),
                          (t.x += Math.sin(r) - 0.5),
                          (t.y +=
                            0.5 * (Math.cos(r) + t.diameter + confetti.speed)),
                          (t.tilt = 15 * Math.sin(t.tiltAngle))),
                      (t.x > n + 20 || t.x < -20 || t.y > i) &&
                        (e && a.length <= confetti.maxCount
                          ? d(t, n, i)
                          : (a.splice(o, 1), o--));
                })(),
                (function (t) {
                  for (var n, e, i, o, r = 0; r < a.length; r++) {
                    if (
                      ((n = a[r]),
                      t.beginPath(),
                      (t.lineWidth = n.diameter),
                      (i = n.x + n.tilt),
                      (e = i + n.diameter / 2),
                      (o = n.y + n.tilt + n.diameter / 2),
                      confetti.gradient)
                    ) {
                      var l = t.createLinearGradient(e, n.y, i, o);
                      l.addColorStop("0", n.color),
                        l.addColorStop("1.0", n.color2),
                        (t.strokeStyle = l);
                    } else t.strokeStyle = n.color;
                    t.moveTo(e, n.y), t.lineTo(i, o), t.stroke();
                  }
                })(l),
                (o = n - (u % confetti.frameInterval))),
                requestAnimationFrame(c);
            }
        }
        function s(t, n, o) {
          var r = window.innerWidth,
            u = window.innerHeight;
          window.requestAnimationFrame =
            window.requestAnimationFrame ||
            window.webkitRequestAnimationFrame ||
            window.mozRequestAnimationFrame ||
            window.oRequestAnimationFrame ||
            window.msRequestAnimationFrame ||
            function (t) {
              return window.setTimeout(t, confetti.frameInterval);
            };
          var m = document.getElementById("confetti-canvas");
          null === m
            ? ((m = document.createElement("canvas")).setAttribute(
                "id",
                "confetti-canvas"
              ),
              m.setAttribute(
                "style",
                "display:block;z-index:124000;pointer-events:none;position:fixed;top:0"
              ),
              document.body.prepend(m),
              (m.width = r),
              (m.height = u),
              window.addEventListener(
                "resize",
                function () {
                  (m.width = window.innerWidth),
                    (m.height = window.innerHeight);
                },
                !0
              ),
              (l = m.getContext("2d")))
            : null === l && (l = m.getContext("2d"));
          var s = confetti.maxCount;
          if (n)
            if (o)
              if (n == o) s = a.length + o;
              else {
                if (n > o) {
                  var f = n;
                  (n = o), (o = f);
                }
                s = a.length + ((Math.random() * (o - n) + n) | 0);
              }
            else s = a.length + n;
          else o && (s = a.length + o);
          for (; a.length < s; ) a.push(d({}, r, u));
          (e = !0), (i = !1), c(), t && window.setTimeout(w, t);
        }
        function w() {
          e = !1;
        }
      })();
      // custom js
      confetti.start();
      setTimeout(confetti.stop, 5000);
    }
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
  width: 560px;
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -49%);
}

.canvas {
  width: 500px;
  height: 500px;
}

@media screen and (max-width: 768px) {
  .canvas {
    width: 320px;
    height: 320px;
    margin: 0 auto !important;
  }
  .round {
    width: 365px;
    position: absolute;
    left: 50%;
    top: 55%;
    transform: translate(-50%, -53%);
  }
}
</style>
