<template>
  <option :style="isClicked ? { 'color': '#45ed32' } : null" @click="toggleIsClicked">
    {{sectorName}}
  </option>
</template>

<script>
export default {
  name: 'CustomOption',
  props: ['sectorName', 'sectorObj'],
  data () {
    return {
      isClicked: false
    }
  },
  methods: {
    toggleIsClicked () {
      this.isClicked = !this.isClicked
      this.$emit('event', this.sectorObj)
    }
  }
}
</script>
