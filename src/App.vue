<template>
  <div id="app">
    <b-container>
      <Cart />
    </b-container>
  </div>
</template>

<script>
import Cart from "./components/Cart.vue";

export default {
  name: "App",
  components: {
    Cart
  }
};
</script>

<style>
body , html , * {
  margin: 0;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  align-items: center;
  justify-content: center;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  display: flex;
  width: 100vw;
  height: 100vh;
}
</style>
