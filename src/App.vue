<template>
  <div id="app">
    <cloth-customizer />
  </div>
</template>

<script>
import ClothCustomizer from './components/ClothCustomizer.vue'

export default {
  name: 'App',
  components: {
    ClothCustomizer
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
