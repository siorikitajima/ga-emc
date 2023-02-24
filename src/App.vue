<template>
  <div>
    <Home :Scores="scores" :Questions="questions" :Counties="counties" :EmcCta="emccta" :QCta="questioncta" v-if="isLoaded"/>
  </div>
</template>

<script>
import Home from './views/Home.vue';
import EMCListing from './assets/json/alldata.json';

export default {
  name: 'App',
  data() {
    return {
      isLoaded: false,
      scores: EMCListing[0].data,
      questions: EMCListing[1].data,
      counties: EMCListing[2].data,
      emccta: EMCListing[3].data,
      questioncta: EMCListing[4].data
    }
  },
  mounted() {
    this.isLoaded = true
  },
  components: { Home }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Open+Sans:wght@400;800&display=swap');

  html, body {
    width: 100%;
    height: 100%;
    margin: 0; padding: 0;
    top: 0; left: 0;
  }
  .container {
    max-height: 100%;
    max-height: 100%;
    /* position: absolute; */
    margin: 0; padding: 0;
    top: 0; left: 0;
  }
#app {
  font-family: 'Open Sans', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #444;
  width: 100%;
  height: 100%;
  margin: 0; padding: 0;
  top: 0; left: 0;
}

nav {
  position: fixed;
  padding: 30px;
}

nav a {
  font-weight: bold;
  color: #444;
}

nav a.router-link-exact-active {
  color: #F39237;
}
</style>
