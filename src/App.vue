<template>
  <div class="main">
    <FortuneWheel
      ref="child"
      @onCanvas="onCanvasRotateStart"
      @onRotateEnd="onRotateEnd"
      v-if="allSpinRewards.length"
      :prizes="allSpinRewards"
      :prizeId="2"
    />

    <ModalDialog
      @visible="visible"
      :statusVisible="showModal"
      :title="modalData.title"
      :message="modalData.message"
      :discountCode="modalData.discountCode"
    />

    <button @click="onCanvasRotateStart"> SPIN </button>
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
      prizeId : null ,
      showModal: false,
      modalData: {
        title: "",
        message: "",
        discountCode: "",
      },
      allSpinRewards: [
        { id: 0, name: "پوچ", value: 5, probability: 30, bgColor : '#ccc' },
        { id: 1, name: "100 امتیاز", value: 5, probability: 20, bgColor : '#fff' },
        { id: 2, name: "10 بن", value: 5, probability: 15, bgColor : '#ccc' },
        { id: 3, name: "3$ wallet", value: 5, probability: 10, bgColor : '#fff' },
        { id: 4, name: "30% discount", value: 5, probability: 10, codePesentCopon: "zghruigh", bgColor : '#ccc' },
        { id: 5, name: "3$ discount", value: 5, probability: 10, bgColor : '#fff' },
      ],
    };
  },
  
  methods: {
    async onCanvasRotateStart() {
      this.$refs.child.spinWheel(2);
    },

    onRotateEnd() {
      // const selectedPrize = this.allSpinRewards[this.finalPrize];
      // if (selectedPrize.name === "پوچ") {
      //   this.modalData = {
      //     title: "😞 متأسفیم!",
      //     message: "متأسفانه این بار چیزی برنده نشدی، اما شانست رو دوباره امتحان کن! 🎡",
      //   };
      // } else {
      //   this.modalData = {
      //     title: "🎉 تبریک! 🎉",
      //     message: `شما برنده ${selectedPrize.name} شدید! 🎁`,
      //     discountCode: selectedPrize.codePesentCopon || "",
      //   };
      // }
      // this.showModal = true;
    },

    visible(toggle) {
      this.showModal = toggle;
    },
  },
};
</script>