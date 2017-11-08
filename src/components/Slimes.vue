<template lang="pug">
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
</template>
