<template>
  <div id="app">
    <Solidus/>
  </div>
</template>

<script>
import Solidus from './components/Solidus'

export default {
  name: 'App',
  components: {
    Solidus
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
.layout {
  display: flex;
}
.flex {
  flex: 0 1 auto;
}
</style>
