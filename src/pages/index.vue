<template>
  Hello
  <!-- <input v-maska="'###.###.###-##'"> -->
</template>

<script>

import { maska } from 'maska'

export default {
  directives: { maska },
  mounted() {
    const user = localStorage.getItem('user-info')
    if (!user)
      this.$router.push('/login')
  },
}
</script>

<!-- <route lang="yaml">
meta:
  layout: home
</route> -->
