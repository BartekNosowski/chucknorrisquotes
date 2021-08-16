<template>
  <div class="header">Chuck Norris Quotes</div>
  <div class="container container-special bg-dark border border-radius" @scroll="onScroll">
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
      scrolledToBottom: false
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
    onScroll ({ target: { scrollTop, clientHeight, scrollHeight }}) {
      if (scrollTop + clientHeight >= scrollHeight) {
        this.getRandom10Quotes()
      }
    }
  },
  mounted() {
    this.getRandom10Quotes();
  },

}
</script>

<style scoped>
</style>
