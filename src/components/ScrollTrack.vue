<template>
  <div class="scrollWrapper">
    <slot></slot>
    <div class="scrollTrack" v-if="getPageHeightPercent < 1">
      <div class="scrollThumb" :style="{height: `${getThumbHeight}px`, transform: `translate3d(0px, ${getThumbPosition}px, 0px)`}"></div>
    </div>
  </div>
</template>

<script>

const ScrollTrack = {
  props: {
    page: Object
  },
  data() {
    return {
      scrollThumb: {
        height: 0,
        position: 0
      }
    }
  },
  methods: {

  },
  computed: {
    getPageHeightPercent() {
      return this.page.height > 1 ? window.innerHeight/this.page.height : window.innerHeight;
    },
    getThumbHeight() {
      this.scrollThumb.height = this.getPageHeightPercent * window.innerHeight;
      return this.scrollThumb.height;
    },
    getThumbPosition() {
      let availableScroll = window.innerHeight - this.scrollThumb.height;
      this.scrollThumb.position = availableScroll * this.page.percent;
      return this.scrollThumb.position;
    }
  },
  created() {

  },
  mounted() {
    this.$nextTick(function () {

    });
  },
  beforeUnmount() {

  }

}
export default ScrollTrack;
</script>

<style scoped>

.scrollTrack {
  position: fixed;
  top: 0;
  right: 0;
  height: 100vh;
  width: 5px;
  background-color: rgba(255, 255, 255, 0.1);
}

.scrollThumb {
  position: fixed;
  height: 0;
  width: 5px;
  background-color: rgba(255, 255, 255, 1);
  border-radius: 5px;
}

</style>