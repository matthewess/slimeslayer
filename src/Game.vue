<template lang="pug">
section.game
  .container
    section.slimes
      .container.columns(v-if='running')
        .column.col-3.col-ml-auto
          ul.menu
            li.divider(:data-content='slimeName')
            li.menu-item status: {{ slimeStatus }}
            li.menu-item
              progress.progress(:value='slimeHealth', max='200')
              sup sp: {{ slimeHealth }} / 200
    section.players
      .container.columns(v-if='!running')
        .column.col-4
          ul.menu
            li.divider(data-content='slimeslayer')
            li.menu-item click the button to begin
            li.menu-item
              button.btn.btn-lg(@click='startGame') start game
      .container.columns(v-if='running')
        .column.col-3
          ul.menu
            li.divider(data-content='sword')
            .columns
              .column
                li.menu-item
                  button.btn(@click='attack', :disabled='swordInactive') attack
                li.menu-item
                  button.btn(@click='defend', :disabled='swordInactive') defend
              .column
                li.menu-item
                  button.btn(@click='charge', :disabled='swordInactive') charge attack
                li.menu-item
                  button.btn(@click='run', :disabled='swordInactive') run
            li.menu-item
              progress.progress(:value='swordHealth', max='100')
              sup hp: {{ swordHealth }} / 100
        .column.col-3
          ul.menu
            li.divider(data-content='staff')
            .columns
              .column
                li.menu-item
                  button.btn(@click='magic', :disabled='staffInactive') magic
                li.menu-item
                  button.btn(@click='heal', :disabled='staffInactive') heal
              .column
                li.menu-item
                  button.btn(@click='burst', :disabled='staffInactive') burst attack
                li.menu-item
                  button.btn(@click='run', :disabled='staffInactive') run
            li.menu-item
              progress.progress(:value='staffHealth', max='100')
              sup hp: {{ staffHealth }} / 100
    section.log
      .container(v-if='running')
        .panel.bg-gray
          .panel-body
            ul
              li(v-for='line in log') {{ line }}
</template>

<script>
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
      swordHealth: 100,
      swordDelay: 0,
      staffHealth: 100,
      staffDelay: 0,
      slimeHealth: 200,
      slimeName: '',
      slimeStatus: '',
      defend: 0,
      log: [],
    };
  },
  methods: {
    random(a) {
      return a[Math.floor(Math.random() * a.length)];
    },
    getDamage(min, max) {
      return Math.floor(Math.random() * (Math.floor(max) - Math.ceil(min))) + Math.ceil(min);
    },
    monsterAttack() {
      let damage = this.getDamage(9, 16) - this.defend;
      this.log.unshift(`the slime slimes the sword for ${damage} damage`);
      this.swordHealth -= damage;
      damage = this.getDamage(9, 16) - this.defend;
      this.log.unshift(`the slime slimes the staff for ${damage} damage`);
      this.staffHealth -= damage;
      this.swordDelay -= 1;
      this.staffDelay -= 1;
      this.defend = 0;
    },
    startGame() {
      this.running = true;
      this.log.unshift('a wild slime appears!');
      const slim = this.random(slims);
      const slem = this.random(slems);
      this.slimeName = `${slim} ${slem}`;
      this.slimeStatus = this.random(statuses);
      this.slimeHealth = 200;
      this.swordHealth = 100;
      this.swordDelay = 0;
      this.staffHealth = 100;
      this.staffDelay = 0;
    },
    attack() {
      const damage = this.getDamage(5, 9);
      this.log.unshift(`sword deals ${damage} damage to the slime`);
      this.slimeHealth -= damage;
      this.swordDelay = 1;
    },
    defend() {
      const defend = this.getDamage(5, 15);
      this.log.unshift(`sword prepares to guard for ${defend} damage`);
      this.defend = defend;
      this.swordDelay = 1;
    },
    charge() {
      const damage = this.getDamage(10, 25);
      this.log.unshift(`sword charges for ${damage} damage to the slime!`);
      this.slimeHealth -= damage;
      this.swordDelay = 2;
    },
    magic() {
      const damage = this.getDamage(4, 12);
      this.log.unshift(`staff deals ${damage} damage to the slime`);
      this.slimeHealth -= damage;
      this.staffDelay = 1;
    },
    heal() {
      const heal = this.getDamage(5, 15);
      this.log.unshift(`staff heals for ${heal} health`);
      if (this.staffHealth + heal > 100) {
        this.staffHealth = 100;
      } else {
        this.staffHealth += heal;
      }
      if (this.swordHealth + heal > 100) {
        this.swordHealth = 100;
      } else {
        this.swordHealth += heal;
      }
      this.staffDelay = 1;
    },
    burst() {
      const damage = this.getDamage(10, 25);
      this.log.unshift(`staff bursts for ${damage} damage to the slime!`);
      this.slimeHealth -= damage;
      this.staffDelay = 2;
    },
    run() {
      this.running = false;
      this.log = [];
    },
  },
  computed: {
    swordInactive() {
      return this.swordDelay > 0 || this.swordHealth <= 0;
    },
    staffInactive() {
      return this.staffDelay > 0 || this.staffHealth <= 0;
    },
    bothInactive() {
      return this.staffInactive && this.swordInactive;
    },
  },
  watch: {
    bothInactive(val) {
      if (this.swordHealth <= 0 && this.staffHealth <= 0) {
        this.running = false;
        this.log = [];
      }
      if (val) {
        while (this.bothInactive) {
          this.slimeStatus = this.random(statuses);
          this.monsterAttack();
        }
      }
    },
  },
};
</script>

<style lang="sass" scoped>
.panel
  min-height: 15px
  max-height: 120px
.btn-lg
  margin: 15px
li
  list-style: none
</style>
