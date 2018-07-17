<template lang="pug">
input(type="checkbox" :checked="!!value" @input="onInput")
</template>

<script>
export default {
  props: {
    value: { type: Number, default: 0 }
  },
  methods: {
    onInput(e) {
      this.$emit('input', e.target.checked ? 1 : 0)
    }
  }
}
</script>

<style lang="stylus" scoped>

</style>
