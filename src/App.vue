<template>
  <ChuckQuotes/>
</template>

<script>
import ChuckQuotes from './components/ChuckQuotes.vue'

export default {
  name: 'App',
  components: {
    ChuckQuotes
  }
}
</script>

<style>
@import './assets/styles/style.css';
#app {
  font-family: Avenir, Helvetica, Arial, Reckless sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
