<template>
  <div class="scrollContentWrapper">
    <slot></slot>
  </div>
</template>

<script>
import * as dat from 'dat.gui';

const pageEndScrollLasting = 0.9999;

const SmoothScroll = {

  data() {
    return {
      sections: 10,
      scrollSpeed: 0,
      pageScrollSpeed: 0,
      pageScrollPosition: 0,
      wheelMaxSpeed: 300,
      touchMaxSpeed: 1500,
      scrollLasting: 0.85,
      endSmoothness: 4,
      lastTouchPos: 0,
      touchSpeed: 0,
      touchLasting: 0.97,
      lastLastingValues: null,
      currentSpeed: 0,
      gui: null,
      page: {
        height: 0,
        percent: 0,
        scrollSpeed: 0
      },
      speedReducer: 0.5
    }
  },
  methods: {
    onWheel(e) {
      if(Math.sign(this.scrollSpeed) === Math.sign(e.deltaY)) {
        this.scrollSpeed += e.deltaY * 0.0002;
      }
      else {
        this.scrollSpeed = e.deltaY * 0.0002;
      }
    },
    onTouchStart(e) {
      this.lastTouchPos = e.touches[0].clientY;
    },
    onTouchMove(e) {
      let diff = (this.lastTouchPos - e.touches[0].clientY);

      if(Math.sign(this.touchSpeed) === Math.sign(diff)) {
        this.touchSpeed += diff * 0.0002;
      }
      else {
        this.touchSpeed = diff * 0.0002;
      }

      this.lastTouchPos = e.touches[0].clientY;
    },
    animate(time) {
      time *= .001;

      const pageBoundingRect = this.$el.getBoundingClientRect();
      let pageHeight = pageBoundingRect.height - window.innerHeight;
      // console.log(pageHeight);

      let pagePercent = pageHeight > 0 ? this.pageScrollPosition/pageHeight : 0;

      this.page.height = pageBoundingRect.height;
      this.page.percent = pagePercent;

      let cumulativeScrollSpeed = this.scrollSpeed + this.touchSpeed;

      // Calculating current scroll speed depending on scroll direction
      if(cumulativeScrollSpeed > 0) {
        this.currentSpeed = this.calcCurrentScrollSpeed(Math.min(1, (pageHeight - this.pageScrollPosition)/window.innerHeight));
      }
      else {
        this.currentSpeed =  this.calcCurrentScrollSpeed(Math.min(1, this.pageScrollPosition/window.innerHeight));
      }

      if(this.currentSpeed === 0) {
        this.scrollSpeed = 0;
        this.touchSpeed = 0;
      }

      // Switching scroll "reducing speed" depending on page location
      if(this.currentSpeed < 0.6 && this.currentSpeed > 0) {
        if(this.touchLasting !== pageEndScrollLasting && this.scrollLasting !== pageEndScrollLasting) {
          this.lastLastingValues = {
            touch: this.touchLasting,
            scroll: this.scrollLasting
          };
        }
        this.touchLasting = pageEndScrollLasting;
        this.scrollLasting = pageEndScrollLasting;
      }
      else{
        if(this.lastLastingValues !== null) {
          this.touchLasting = this.lastLastingValues.touch;
          this.scrollLasting = this.lastLastingValues.scroll;
          this.lastLastingValues = null;
        }
      }

      let cumulativeSpeed = (this.scrollSpeed * this.wheelMaxSpeed) + (this.touchSpeed * this.touchMaxSpeed);

      // Calculate page scrolling speed
      this.pageScrollSpeed = Math.sign(cumulativeSpeed) * Math.pow(Math.abs(cumulativeSpeed), this.currentSpeed) * this.currentSpeed;
      this.page.scrollSpeed = this.pageScrollSpeed;

      // Calculate page scroll position
      this.pageScrollPosition +=  this.pageScrollSpeed;

      // Calculate page scroll position if outside page
      if(this.pageScrollPosition < 0){
        this.pageScrollPosition = 0;
      }
      else if(this.pageScrollPosition > pageHeight && pageHeight > 0) {
        this.pageScrollPosition = pageHeight;
      }

      // Reduce scrolling speed
      this.touchSpeed *= this.touchLasting;
      this.scrollSpeed *= this.scrollLasting;

      // Pass values to parent
      this.$emit('changeScrollValues', {
        scrollSpeed: this.scrollSpeed,
        pageScrollSpeed: this.pageScrollSpeed,
        pageScrollPosition: this.pageScrollPosition,
        page: this.page,
        sections: this.sections
      });

      // Make the page scroll
      this.$el.style.transform = `translate3d(0px, ${-this.pageScrollPosition}px, 0px)`;

      requestAnimationFrame(this.animate);
    },
    calcCurrentScrollSpeed(pagePercent) {
      let m = 1 - Math.abs(Math.pow(( pagePercent - 1), this.endSmoothness));
      return Math.max(0, Math.sign(m) * Math.pow(m,this.speedReducer));
    }
  },
  created() {
    window.addEventListener('wheel', this.onWheel);
    window.addEventListener('touchstart', this.onTouchStart);
    window.addEventListener('touchmove', this.onTouchMove);
  },
  mounted() {
    this.$nextTick(function () {

      requestAnimationFrame(this.animate);

      this.gui = new dat.gui.GUI();

      this.gui.add(this, 'sections').min(0).max(15).step(1);
      this.gui.add(this, 'wheelMaxSpeed').min(1).max(3000).step(1);
      this.gui.add(this, 'touchMaxSpeed').min(1).max(3000).step(1);
      this.gui.add(this, 'scrollLasting').min(0.001).max(0.999).step(0.001);
      this.gui.add(this, 'touchLasting').min(0.001).max(0.999).step(0.001);
      this.gui.add(this, 'endSmoothness').min(1).max(200).step(1);
      this.gui.add(this, 'speedReducer').min(0.1).max(1).step(0.1);

    })
  },
  beforeUnmount() {
    window.removeEventListener('wheel', this.onWheel);
    window.addEventListener('touchstart', this.onTouchStart);
    window.removeEventListener('touchmove', this.onTouchMove);
    this.gui.destroy()
  }
}

export default SmoothScroll
</script>

<style scoped>

.scrollContentWrapper {
  position: relative;
  will-change: transform;
}

</style>