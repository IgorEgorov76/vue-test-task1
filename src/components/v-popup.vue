<template>
  <div v-if="isOpen" class="backdrop">
    <div class="popup">
      <div class="popup__text">Вы уверены, что хотите удалить пользователя?</div>
      <div class="popup__btn-block">
        <button @click="confirm" class="popup__btn-block-item">Да</button>
        <button @click="close" class="popup__btn-block-item">Нет</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    isOpen: {
      type: Boolean,
      required: true
    }
  },
  emits: {
    ok: null,
    close: null
  },
  mounted() {
    document.addEventListener('keydown', (e) => {
      if (this.isOpen && e.key === 'Escape') {
        this.close()
      }
    })
  },
  methods: {
    close() {
      this.$emit('close')
    },
    confirm() {
      this.$emit('ok')
    }
  }
}
</script>

<style scoped>

.popup {
  font-family: 'Inter', sans-serif;
  font-weight: 400;
  font-size: 12px;
  width: 353px;
  height: 134px;
  left: 50%;
  transform: translateX(-50%);
  position: fixed;
  top: 150px;
  background-color: white;
  z-index: 101;
  border-radius: 8px;
}

.popup__text {
  padding: 34px 34px 0px 34px;
  margin-bottom: 24px;
}

.backdrop {
  position: fixed;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
  background-color: rgba(0, 0, 0, 0.8);
  z-index: 100;
}

.popup__btn-block {
  display: flex;
  justify-content: center;
}

.popup__btn-block-item {
  width: 95px;
  height: 27px;
  color: #828282;
  background-color: #E0E0E0;
  border-radius: 3px;
  border: none;
  cursor: pointer;
}

.popup__btn-block-item:not(:last-child) {
  margin-right: 36px;
}

.popup__btn-block-item:hover {
  color: #FFFFFF;
  background-color: #0CB4F1;
}
</style>

