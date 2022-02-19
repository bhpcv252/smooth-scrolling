<template>
  <div class="appWrapper">
    <ScrollTrack :page="page">
      <SmoothScroll @changeScrollValues="setScrollValues($event)">
        <Section v-for="i in sections" :number="i" :key="i">
          <!--        <p>scrollSpeed: {{ scrollSpeed }}</p>
                  <p>pageScrollSpeed: {{ pageScrollSpeed }}</p>
                  <p>pageScrollPosition: {{ pageScrollPosition }}</p>-->
          <p>{{ i }}</p>
          <p>{{ page.height }}</p>
        </Section>
      </SmoothScroll>
    </ScrollTrack>
  </div>
</template>

<script>
import Section from './components/Section.vue';
import ScrollTrack from './components/ScrollTrack.vue';
import SmoothScroll from './components/SmoothScroll.vue';

const App = {
  components: {
    Section,
    ScrollTrack,
    SmoothScroll
  },
  data() {
    return {
      sections: 0,
      scrollSpeed: 0,
      pageScrollSpeed: 0,
      pageScrollPosition: 0,
      page: {
        height: 0
      }
    }
  },
  methods: {
    setScrollValues(scrollValues) {
      this.sections = scrollValues.sections;
      this.scrollSpeed = scrollValues.scrollSpeed;
      this.pageScrollSpeed = scrollValues.pageScrollSpeed;
      this.pageScrollPosition = scrollValues.pageScrollPosition;
      this.page = scrollValues.page;
    }
  }

}

export default App

</script>

<style>

*, ::after, ::before {
  box-sizing: border-box;
}

body, html {
  margin: 0;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  overflow: hidden;
}

p {
  margin: 0 0 1rem;
  padding: 1rem;
  font-size: 3rem;
}

</style>
