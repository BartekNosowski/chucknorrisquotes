<template>
  <div class="header">Chuck Norris Quotes</div>
  <div class="container bg-dark border border-radius">
    <button @click="getRandom10Quotes">10Q</button>
      <span :key="quotes.id" v-for="quotes in quotesArray" class="row m-1 px-1 text-center bg-info text-white">{{ quotes.value }}</span>
  </div>
</template>
<script>
import axios from 'axios';

export default {
  name: "Users",
  data() {
    return {
      quotesArray: [],
    }
  },
  methods: {
    getAQuote() {
      axios.get('https://api.chucknorris.io/jokes/random')
          .then(res => {
            this.quotesArray.push(res.data)
          })
    },
    getRandom10Quotes() {
      for (let i = 0; i < 10; i++) {
        this.getAQuote()
      }
    },
  },
  mounted() {
    this.getRandom10Quotes();
  },

}
</script>

<style scoped>
</style>
