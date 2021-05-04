<template>
  <option :style="isClicked ? { 'color': '#45ed32' } : null" @click="toggleIsClicked">
    {{formatName()}}
  </option>
</template>

<script>
export default {
  name: 'CustomOption',
  props: ['sectorObj'],
  data () {
    return {
      spacingMultiplier: 4,
      isClicked: false
    }
  },
  methods: {
    toggleIsClicked () {
      this.isClicked = !this.isClicked
      this.$emit('event', this.sectorObj)
    },
    formatName () {
      return '\xa0'.repeat(this.spacingMultiplier * this.sectorObj.spaces_count) + this.sectorObj.name
    }
  }
}
</script>
