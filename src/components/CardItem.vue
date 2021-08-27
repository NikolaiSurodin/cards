<template>
  <div class="wrapper" @click="clickOnCard">
    <p>
      <slot name="front"></slot>
    </p>
    <p>
      <slot name="back"></slot>
    </p>
  </div>
</template>

<script>

export default {
  name: "CardItem",
  data() {
    return {}
  },
  methods: {
    clickOnCard() {
      this.$emit( 'clickOnCard' )
    }
  }
}
</script>

<style scoped>
.wrapper {
  height: 169px;
  display: flex;
  justify-content: center;
  align-items: center;
  background: radial-gradient(61% 73%, #d0d0d0,#77e68f);
}
.wrapper p {
  color: aliceblue;
  font-size: 70px;
}
.wrapper:hover {
  cursor: pointer;
}
</style>