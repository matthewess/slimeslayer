<template lang="pug">
.container.columns
  .column.col-4
    ul.menu
      li.divider(data-content='slimeslayer')
      li.menu-item click the button to begin
      li.menu-item
        button.btn.btn-lg(@click='startGame') start game
</template>

<script>
export default {
  name: 'Menu',
};
</script>
