<template>
  <div id="app">
    <Calculator
      v-if="showCalculator"
      :initialWindowPosition="{ x: 200, y: 100 }"
      @close="close"
      @minimize="minimize"
      @expand="expand"
    />
  </div>
</template>

<script>
import Calculator from "./components/Calculator.vue";

/* TODO
- fix bug: 6 divide 9 = C anyOp(?) causes infinity
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
- separate calculator into 2 components; one for calculator and another for wrapping that in a draggable window.
- new taskbar-ish component that has icon for the app, handles window events properly (min, expand, close), and animates transition.
  - has props for setting icon position and initial window position.
- make all this into generic windowing system components, of which the calculator is an example of it being used.
  - has slots for the window's content, e.g. calculator content or any other components.
  - has slots(or props?) for custom child events that cause dragging (e.g. calculator display can cause dragging of parent window).
  - potential components: AppWindow, AppTaskBar, AppIcon, CalculatorApp, CalculatorContent
- get storybook up in this heezie to handle these components
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
