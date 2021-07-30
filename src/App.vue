<template>
  <DiceRoll/>
</template>

<script>
import DiceRoll from './components/DiceRoll.vue'

export default {
  name: 'App',
  components: {
    DiceRoll
  }
}
</script>

<style>
@import './assets/styles/style.css';
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
