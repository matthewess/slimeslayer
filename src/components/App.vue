<template lang="pug">
#app
  .container
    transition(name='fade', mode='out-in')
      Menu(v-if='running')
      Game(v-else)
</template>

<script>
import Game from './Game.vue';
import Menu from './Menu.vue';

export default {
  name: 'App',
  components: {
    Game,
    Menu,
  },
};
</script>

<style lang="sass">
@import '../../node_modules/spectre.css/dist/spectre.css'
@import '../../node_modules/spectre.css/dist/spectre-exp.css'
@import '../../node_modules/spectre.css/dist/spectre-icons.css'
@import '../../node_modules/animate.css/animate.css'

.container
  padding: 30px
</style>
