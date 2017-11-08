<template lang="pug">
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
</template>
