<template>
  <Dictionary/>
</template>

<script>
import Dictionary from './components/Dictionary.vue'

export default {
  name: 'App',
  components: {
    Dictionary,
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
