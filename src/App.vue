<template>
  <div class="main">
    <FortuneWheel
      ref="child"
      @prizesRandomIndex="prizeIndex"
      @onCanvas="onCanvasRotateStart"
      @onRotateEnd="onRotateEnd"
      v-if="allSpinRewards.length"
      :prizes="allSpinRewards"
      :prizesId="0"
      :soundStatus="true"
      :imgBorder="true"
      :styleWheel="styleWheel"
      :happyRain="true"
    />

    <ModalDialog
      @visible="visible"
      :statusVisible="showModal"
      :title="modalData.title"
      :message="modalData.message"
      :discountCode="modalData.discountCode"
    />

    <button class="StartBtn" @click="onCanvasRotateStart">SPIN</button>
  </div>
</template>

<script>
import FortuneWheel from "./components/FortuneWheel.vue";
import ModalDialog from "./components/modalDialog.vue";

export default {
  components: {
    FortuneWheel,
    ModalDialog,
  },

  data() {
    return {
      prizeId: null,
      showModal: false,
      randomId: null,
      modalData: {
        title: "",
        message: "",
        discountCode: "",
      },
      styleWheel: {
        colorText: "black",
        twoLineText: {
          status:false,
          outlineColor: "#000",
          inlineColor: "#fff",
          lineWidth: 3,
        },
        font: "bold 16px Arial",
        textAlign: "center",
        textBaseline: "middle",
        textOrientation: "vertical",
        // textOrientation : 'horizontal',
        shadow: {
          shadowColor: "rgba(0, 0, 0, 0.5)",
          shadowBlur: 14, // shadow blur level
          shadowOffsetX: 0, // shadow offset in X axis
          shadowOffsetY: 0, // shadow offset in Y axis
        },
        // stroke:{
        //   lineWidth : 10,
        //   color : '#000'
        // }
      },

      allSpinRewards: [
        { id: 0, name: "Nothing", value: 5, probability: 30, bgColor: "#00afad" },
        {
          id: 1,
          name: "100 Points",
          value: 5,
          probability: 20,
          bgColor: "#fff",
        },
        { id: 2, name: "10 Voucher", value: 5, probability: 15, bgColor: "#f173ac" },
        {
          id: 3,
          name: "$3 Wallet",
          value: 5,
          probability: 10,
          bgColor: "#00afad",
        },
        {
          id: 4,
          name: "30% Discount",
          value: 5,
          probability: 10,
          codePesentCopon: "zghruigh",
          bgColor: "#fff",
        },
        {
          id: 5,
          name: "$3 Discount",
          value: 5,
          probability: 10,
          bgColor: "#f173ac",
        },
      ],
    };
  },

  methods: {
    async onCanvasRotateStart() {
      this.$refs.child.spinWheel();
    },

    onRotateEnd() {
      const selectedPrize = this.randomId;
      console.log(selectedPrize, "selectedPrize");
      if (selectedPrize.name === "Nothing") {
        this.modalData = {
          title: "😞 Sorry!",
          message:
            "Unfortunately, you didn’t win anything this time, but try your luck again! 🎡",
        };
      } else {
        this.modalData = {
          title: "🎉 Congratulations! 🎉",
          message: `You won ${selectedPrize.name}! 🎁`,
          discountCode: selectedPrize.codePesentCopon || "",
        };
      }
      this.showModal = true;
    },

    prizeIndex(index) {
      //if you need prize index when random choose
      console.log(index, "prizesRandomIndex : ");
      this.randomId = index;
    },

    visible(toggle) {
      this.showModal = toggle;
    },
  },
};
</script>
<style>
.StartBtn{
  padding: 10px;
  width: 100px;
  cursor: pointer;
  background-color: pink;
}
.main{
  width: 100%;
  display: flex;
  align-items: center;
  flex-direction: column;
  gap: 30px;
  justify-content: center;
  height: 100vh;
}
</style>
