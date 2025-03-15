<template>
  <Teleport to="body">
    <div v-if="statusVisible" class="modal-overlay">
      <div class="modal-content" >
        <h2>{{title}}</h2>
        <p>{{message}}</p>

        <div v-if="discountCode" class="code-container">
          <span>{{ discountCode }}</span>
          <button class="copy" @click="copyCode">Copy</button>
        </div>

        <div class="modal-actions">
          <button @click="closeModal" class="btn-confirm">Okay</button>
        </div>
      </div>
    </div>
  </Teleport>
</template>

<script>
export default {
props: {
  statusVisible: Boolean,
  title: String,
  message: String,
  discountCode: String,
},
methods: {
  closeModal() {
    this.$emit("visible", false);
  },
 
  copyCode() {
    navigator.clipboard.writeText(this.discountCode).then(() => {
      alert("Discount code copied!");
    });
  },
},
};
</script>

  
  <style scoped>
  h2{
    margin-bottom: 30px;
  }
  .modal-overlay {
    position: fixed;
    inset: 0;
    background: rgba(0, 0, 0, 0.5);
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 10;
  }
  
  .modal-content {
    background: white;
    padding: 40px;
    border-radius: 10px;
    text-align: center;
    width: 400px;
  }
  
  .code-container {
    display: flex;
    justify-content: space-between;
    align-items: center;
    background: #f1f1f1;
    padding: 15px;
    margin: 10px 0;
    border-radius: 5px;
  }
  
  .modal-actions {
  }
  
  .btn-confirm {
    background: #faabcf;
    width: 80px;
    border: none;
    padding: 10px 15px;
    cursor: pointer;
    border-radius: 5px;
  }
  .copy{
    background-color: #faabcf ;
    width: 57px;
    padding: 3px;
    border-radius: 5px;
  }
  </style>
  