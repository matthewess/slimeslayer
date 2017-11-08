<template lang="pug">
#app
  .container
    Game
</template>

<script>
import Game from './Game.vue';

export default {
  name: 'App',
  components: {
    Game,
  },
};
</script>

<style lang="sass">
@import '../node_modules/spectre.css/dist/spectre.css'
@import '../node_modules/spectre.css/dist/spectre-exp.css'
@import '../node_modules/spectre.css/dist/spectre-icons.css'
@import '../node_modules/animate.css/animate.css'

.container
  padding: 30px
</style>
