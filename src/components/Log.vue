<template lang="pug">
.container
  .panel.bg-gray
    .panel-body
      ul
        template(v-for='line in log')
          li(:class=
            '{ "text-success": line.good, "text-error": !line.good }') {{ line.message }}
</template>
