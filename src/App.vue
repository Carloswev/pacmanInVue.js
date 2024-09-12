<template>
  <div id="app">
    <GameCanvas />
  </div>
</template>

<script>
import GameCanvas from './components/GameCanvas.vue';

export default {
  name: 'App',
  components: {
    GameCanvas
  }
};
</script>

<style>
#app {
  text-align: center;
  background-color: #000;
  color: #fff;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
