<template>
  <div id="app">
    <AstTest/>
  </div>
</template>

<script>
import AstTest from './components/AstTest.vue'

export default {
  name: 'app',
  components: {
    AstTest
  }
}
</script>

<style>
html, body {
  width: 100%;
  height: 100%;
}

body{
  margin: 0 auto;
}
</style>
