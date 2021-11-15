<template>
  <div id="app">
    <Navbar />
    <Conversor />
    <Footer />
  </div>
</template>

<script>

import Navbar from './components/Navbar.vue'
import Conversor from './components/Conversor.vue'
import Footer from './components/Footer.vue'



export default {
  name: 'App',
  components: {
    Navbar,
    Conversor,
    Footer,
    
  }
}
</script>

<style>
@import url("./assets/css/style.css");
</style>
