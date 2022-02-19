<template>
  <div class="section" :style="{backgroundColor: getBgColor}">
    <slot></slot>
  </div>
</template>

<script>
const Section = {
  props: {
    number: Number
  },
  computed: {
    getBgColor() {
      let colors = ["#d32f2f", "#7b1fa2", "#303f9f", "#c2185b", "#9c27b0", "#e65100", "#3e2723", "#212121"];
      let rn = (this.number)%colors.length;
      return colors[rn];
    }
  }
}

export default Section
</script>

<style scoped>

.section {
  width: 100%;
  height: 60vh;
  color: #ffffff;
}

</style>
