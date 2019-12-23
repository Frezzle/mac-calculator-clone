<template>
  <div id="app">
    <Calculator
      v-if="showCalculator"
      @close="close"
      @minimize="minimize"
      @expand="expand"
    />
  </div>
</template>

<script>
import Calculator from "./components/Calculator.vue";

/* TODO
- better +/- symbol for negation
- bigger minus symbol
- same typeface/size/weight as macos calc app font
- better long-string resizing in display, ideally via css, but simple js solution is possible.
- grey circles when not focusing on calc "window"
- improve circles hitbox accuracy
- improve circle symbols size/centering
- port to electron
- publish component to be able to be used in other web apps
- window mode setting; if true then rounded corners and circles
  - can i take advantage of firefox picture in picture mode?
  - make windowed mode not put calc into dom flow. (does it need props (with defaults) for initial position?)
- packagify it, so it can be imported and used in other people's projects.
- show operations on macbook touch bar (electron only i guess? if possible at all)
- can have multiple calc components, all draggable. sort out z-index and shadow on focus.
  - z-index needs to be prop for consumers to decide best range?
*/

export default {
  components: {
    Calculator
  },
  data() {
    return {
      showCalculator: true
    };
  },
  methods: {
    close() {
      console.log("close");
      this.showCalculator = false;
    },
    minimize() {
      console.log("minimize");
    },
    expand() {
      console.log("expand");
    }
  }
};
</script>

<style>
#app {
  font-family: Helvetica, sans-serif;
  padding: 30px;
  height: 100%;
  box-sizing: border-box;
  width: 100%;
  background-color: #333;
  overflow: hidden;
}

html,
body {
  background-color: rgb(34, 34, 34);
  height: 100vh;
  margin: 0;
}
</style>
