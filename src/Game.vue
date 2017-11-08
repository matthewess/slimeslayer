<template lang="pug">
section.game
  transition(name='fade', mode='out-in')
    .container.columns(v-if='!running', key='menu')
          .column.col-4
            ul.menu
              li.divider(data-content='slimeslayer')
              li.menu-item click the button to begin
              li.menu-item
                button.btn.btn-lg(@click='startGame') start game
    .container(v-else, key='game')
      section.slimes
        .container.columns
          .column.col-3.col-mx-auto
            .slime.animated.infinite(:class='slimeAnimation')
          .column.col-3.col-ml-auto
            ul.menu
              li.divider(:data-content='slimeName')
              li.menu-item status: {{ slimeStatus }}
              li.menu-item
                progress.progress(:value='slimeHealth', max='200')
                sup sp: {{ slimeHealth }} / 200
      section.players
        .container.columns
          .column.col-3
            ul.menu
              li.divider(data-content='knight')
              .columns
                .column
                  li.menu-item
                    button.btn(@click='attack', :disabled='knightInactive') attack
                  li.menu-item
                    button.btn(@click='defend', :disabled='knightInactive') defend
                .column
                  li.menu-item
                    button.btn(@click='charge', :disabled='knightInactive') charge attack
                  li.menu-item
                    button.btn(@click='run', :disabled='knightInactive') run
              li.menu-item
                progress.progress(:value='knightHealth', max='100')
                sup hp: {{ knightHealth }} / 100
          .column.col-3
            ul.menu
              li.divider(data-content='mage')
              .columns
                .column
                  li.menu-item
                    button.btn(@click='magic', :disabled='mageInactive') magic
                  li.menu-item
                    button.btn(@click='heal', :disabled='mageInactive') heal
                .column
                  li.menu-item
                    button.btn(@click='burst', :disabled='mageInactive') burst attack
                  li.menu-item
                    button.btn(@click='run', :disabled='mageInactive') run
              li.menu-item
                progress.progress(:value='mageHealth', max='100')
                sup hp: {{ mageHealth }} / 100
      section.log
        .container
          .panel.bg-gray
            .panel-body
              ul
                template(v-for='line in log')
                  li(:class=
                    '{ "text-success": line.good, "text-error": !line.good }') {{ line.message }}
</template>

<script>
// slime names and statuses
const slims = [
  'slimy',
  'slippy',
  'sloppy',
  'spoopy',
  'sploppy',
  'slemmy',
];
const slems = [
  'slimer',
  'slipper',
  'slopper',
  'spooper',
  'splopper',
  'slemmer',
];
const statuses = [
  'slimeful',
  'drooping',
  'hungry',
  'scared',
  'peaceful',
  'energetic',
];

export default {
  name: 'Game',
  data() {
    return {
      running: false,
      knightHealth: 100,
      knightDelay: 0,
      mageHealth: 100,
      mageDelay: 0,
      slimeHealth: 200,
      slimeName: '',
      slimeStatus: '',
      defense: 0,
      log: [],
      disabled: false,
      slimeAnimation: 'rubberBand',
    };
  },
  methods: {
    random(a) {
      // return a random item from an array
      return a[Math.floor(Math.random() * a.length)];
    },
    getDamage(min, max) {
      // generate a random number between ceiling and floor exclusive
      return Math.floor(Math.random() * (Math.floor(max) - Math.ceil(min))) + Math.ceil(min);
    },
    monsterAttack() {
      if (this.slimeHealth <= 0) return;
      // calculate monster damage
      let damage = this.getDamage(9, 16) - this.defense;
      this.log.unshift({ message: `the slime slimes the knight for ${damage} damage`, good: false });
      this.knightHealth -= damage;
      damage = this.getDamage(9, 16) - this.defense;
      this.log.unshift({ message: `the slime slimes the mage for ${damage} damage`, good: false });
      this.mageHealth -= damage;
      // one turn passes
      this.knightDelay -= 1;
      this.mageDelay -= 1;
      this.defense = 0;
      if (this.knightHealth <= 0 && this.mageHealth <= 0) {
        this.end({ message: 'you lost...', good: false });
      }
    },
    startGame() {
      this.running = true;
      this.disabled = false;
      this.log = [];
      this.log.unshift({ message: 'a wild slime appears!', good: false });
      const slim = this.random(slims);
      const slem = this.random(slems);
      this.slimeName = `${slim} ${slem}`;
      this.slimeStatus = this.random(statuses);
      this.slimeHealth = 200;
      this.knightHealth = 100;
      this.knightDelay = 0;
      this.mageHealth = 100;
      this.mageDelay = 0;
    },
    attack() {
      this.slimeAnimation = 'shake';
      const damage = this.getDamage(5, 9);
      this.log.unshift({ message: `knight deals ${damage} damage to the slime`, good: true });
      this.slimeHealth -= damage;
      this.knightDelay = 1;
      const vm = this;
      setTimeout(() => {
        vm.slimeAnimation = 'rubberBand';
      }, 1000);
    },
    defend() {
      const defend = this.getDamage(5, 15);
      this.log.unshift({ message: `knight prepares to guard for ${defend} damage`, good: true });
      this.defense = defend;
      this.knightDelay = 1;
    },
    charge() {
      this.slimeAnimation = 'shake';
      const damage = this.getDamage(10, 25);
      this.log.unshift({ message: `knight charges for ${damage} damage to the slime!`, good: true });
      this.slimeHealth -= damage;
      this.knightDelay = 2;
      this.log.unshift({ message: 'the knight is worn out from charging...', good: false });
      const vm = this;
      setTimeout(() => {
        vm.slimeAnimation = 'rubberBand';
      }, 1000);
    },
    magic() {
      this.slimeAnimation = 'wobble';
      const damage = this.getDamage(4, 12);
      this.log.unshift({ message: `mage deals ${damage} damage to the slime`, good: true });
      this.slimeHealth -= damage;
      this.mageDelay = 1;
      const vm = this;
      setTimeout(() => {
        vm.slimeAnimation = 'rubberBand';
      }, 1000);
    },
    heal() {
      const heal = this.getDamage(5, 15);
      this.log.unshift({ message: `mage heals for ${heal} health`, good: true });
      if (this.mageHealth + heal > 100) {
        this.mageHealth = 100;
      } else {
        this.mageHealth += heal;
      }
      if (this.knightHealth + heal > 100) {
        this.knightHealth = 100;
      } else {
        this.knightHealth += heal;
      }
      this.mageDelay = 1;
    },
    burst() {
      this.slimeAnimation = 'wobble';
      const damage = this.getDamage(10, 25);
      this.log.unshift({ message: `mage bursts for ${damage} damage to the slime!`, good: true });
      this.slimeHealth -= damage;
      this.mageDelay = 2;
      this.log.unshift({ message: 'the mage is worn out from bursting...', good: false });
      const vm = this;
      setTimeout(() => {
        vm.slimeAnimation = 'rubberBand';
      }, 1000);
    },
    run() {
      this.end({ message: 'you ran away...', good: false });
    },
    end(message) {
      this.log.unshift(message);
      this.disabled = true;
      const vm = this;
      setTimeout(() => {
        vm.log = [];
        vm.running = false;
      }, 5000);
    },
  },
  computed: {
    knightInactive() {
      return this.knightDelay > 0 || this.knightHealth <= 0 || this.disabled;
    },
    mageInactive() {
      return this.mageDelay > 0 || this.mageHealth <= 0 || this.disabled;
    },
    bothInactive() {
      return this.mageInactive && this.knightInactive && !this.disabled;
    },
  },
  watch: {
    bothInactive(val) {
      if (val) {
        while (this.bothInactive) {
          this.slimeStatus = this.random(statuses);
          this.monsterAttack();
        }
      }
    },
    slimeHealth(val) {
      if (val <= 0) {
        this.slimeStatus = 'dead';
        this.end({ message: 'you won!!!', good: true });
      }
    },
  },
};
</script>

<style lang="sass" scoped>
.slime
  width: 120px
  height: 180px
  background: radial-gradient(#50C9C3, #96DEDA)
  display: block
  border-radius: 60% 60% 60% 60% / 90% 90% 30% 30%
  animation-duration: 1s

.panel
  min-height: 15px
  max-height: 150px
.btn-lg
  margin: 15px
li
  list-style: none

.fade-enter-active, .fade-leave-active
  transition: opacity .5s
.fade-enter, .fade-leave-to
  opacity: 0
</style>
