<template>
  <div id="app">
    <template v-for="i in count">
      <Calculator
        :key="i"
        :uuid="i"
        v-if="showCalculator[i - 1]"
        @close="() => close(i)"
        @minimize="() => minimize(i)"
        @expand="() => expand(i)"
      />
    </template>
    <div v-for="i in 100" :key="i">
      Lorem ipsum dolor sit amet consectetur adipisicing elit. Pariatur laborum
      voluptates non cum odio ipsum dolores facilis sed aliquid! Ratione.
    </div>
  </div>
</template>

<script>
import Calculator from "./components/Calculator.vue";

/* TODO
- implement the other modes of the osx calculator! :O
- add faint 1px window border.
  - make sure orange buttons overlap this border.
- fix bug: 6 divide 9 = C anyOp(?) causes infinity
- fix bug: pressing 4 - 10 = (+/-) = should give -4, not -24 (the +/- key should update memory)
- better +/- symbol for negation
- bigger minus symbol
- same typeface/size/weight as macos calc app font
- better long-string resizing in display, ideally via css, but simple js solution is possible.
- grey circles when not focusing on calc "window"
- improve circles hitbox accuracy
- improve circle symbols size/centering
  - also colour the symbol; they aren't black in osx calc.
- port to electron
- publish component to be able to be used in other web apps
- window mode setting; if true then rounded corners and circles
  - can i take advantage of firefox picture in picture mode?
  - make windowed mode not put calc into dom flow. (does it need props (with defaults) for initial position?)
- packagify it, so it can be imported and used in other people's projects.
- show operations on macbook touch bar (electron only i guess? if possible at all)
- multiple windows: sort out z-index and shadow on focus.
  - z-index needs to be prop for consumers to decide best range?
- separate calculator into 2 components; one for calculator and another for wrapping that in a draggable window.
- new taskbar-ish component that has icon for the app, handles window events properly (min, expand, close), and animates transition.
  - has props for setting icon position and initial window position.
- make all this into generic windowing system components, of which the calculator is an example of it being used.
  - has slots for the window's content, e.g. calculator content or any other components.
  - has slots(or props?) for custom child events that cause dragging (e.g. calculator display can cause dragging of parent window). this could instead be an object/array prop that takes the element/s which will activate this windows dragging. simply check that the mouse/touch events' target is one of these elements or is the window top bar.
  - potential components: AppWindow, AppTaskBar, AppIcon, CalculatorApp, CalculatorContent
- get storybook up in this heezie to handle these components
- the calc window's grey background and buttons actually let through some colour of the window behind it, blurring/diffusing it. simulate the same?!
*/

export default {
  components: {
    Calculator
  },
  data() {
    return {
      count: 3,
      showCalculator: [true, true, true]
    };
  },
  methods: {
    close(calcNum) {
      console.log("close", calcNum);
      this.showCalculator.splice(calcNum - 1, 1, false);
    },
    minimize(calcNum) {
      console.log("minimize", calcNum);
    },
    expand(calcNum) {
      console.log("expand", calcNum);
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
}

html,
body {
  background-color: rgb(34, 34, 34);
  height: 100vh;
  margin: 0;
}
</style>
