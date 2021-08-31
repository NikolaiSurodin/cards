<template>

  <div class="wrap">
    <div class="popup">
      <header class="header">
      </header>
      <section class="section">
        <p>Вы точно хотите начать?!</p>
      </section>
      <footer class="footer">
        <button class="button" type="button" @click="startGame">Погнали!</button>
        <button class="button restart" type="button" @click="close">Отмена</button>
      </footer>
    </div>

  </div>
</template>

<script>
export default {
  name: "ConfirmPage",
  methods: {
    startGame() {
      this.$emit( 'startGame' )
    },
    close() {
      this.$emit( 'close' )
    }
  },
}
</script>

<style scoped>
.wrap {
  background: rgba(64, 64, 64, .4);
  display: flex;
  justify-content: center;
  align-items: center;
  position: absolute;
  right: 0;
  left: 0;
  top: 0;
  bottom: 0;
}

.button {
  background-color: #8b4cf9;
  border: 1px solid transparent;
  border-radius: 5px;
  padding: 13px 35px;
  color: #ffff;
}

.button + .button {
  margin-left: 15px;
}

.restart {
  background-color: #549254;
}

.button:hover {
  background-color: #ab81fd;
  cursor: pointer;
}

.popup {
  padding: 150px;
  position: center;
  width: 300px;
  background: linear-gradient(#e6e6e6, #9198e5);
  box-shadow: 0 0 17px 0 #e7e7e7;
  z-index: 9999;
}

.footer {
  display: flex;
}

p span {
  font-weight: bold;
}
</style>