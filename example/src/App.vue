<template>
  <div id="app">
    <div class="placeholder"></div>
    <back-to-top :scrollDuration="1000" :bottom="40" :right="40"/>
  </div>
</template>

<script>
import backToTop from '../../src/back2top';

export default {
  name: 'app',
  data () {
    return {
      msg: 'Welcome to Your Vue.js App'
    }
  },
  mounted() {},
  components: {
    backToTop
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.placeholder {
  height: 2000px;
  background: pink;
}
</style>
