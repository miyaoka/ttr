<template lang="pug">
.Index
  main
    TetrisView(
      :width="10"
      :height="20"
    )
</template>

<script>
import TetrisView from '@/components/TetrisView'

export default {
  components: {
    TetrisView
  }
}
</script>

<style lang="stylus" scoped>

</style>
