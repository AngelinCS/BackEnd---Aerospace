<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <!-- Replace HelloWorld with LeafletTest -->
    <LeafletTest />
  </div>
</template>

<script>
// Import the LeafletTest component
import LeafletTest from './components/LeafletTest.vue'

export default {
  name: 'App',
  components: {
    // Register the LeafletTest component
    LeafletTest
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
</style>
