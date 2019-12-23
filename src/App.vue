<template>
  <div id="app">
    <Calculator
      v-if="showCalculator"
      id="calc"
      @close="close"
      @minimize="minimize"
      @expand="expand"
    />
  </div>
</template>

<script>
import Calculator from "./components/Calculator.vue";

/* TODO
- click and drag like a window
- better +/- symbol for negation
- bigger minus symbol
- same typeface/size/weight as macos calc app font
- better long-string resizing in display, ideally via css
- grey circles when not focusing on calc "window"
- improve circles hitbox accuracy
- improve circle symbols size/centering
- port to electron
- publish component to be able to be used in other web apps
- window mode setting; if true then rounded corners and circles
- packagify it, so it can be imported and used in other people's projects.
- show operations on macbook touch bar (electron only i guess? if possible at all)
*/

export default {
  components: {
    Calculator
  },
  data() {
    return {
      showCalculator: true,
      dragItem: null,
      container: null,
      active: false,
      currentX: null,
      currentY: null,
      initialX: null,
      initial: null,
      xOffset: 0,
      yOffset: 0
    };
  },
  mounted() {
    this.dragItem = document.getElementById("calc");
    this.container = document.getElementById("app");

    this.container.addEventListener("touchstart", this.dragStart, false);
    this.container.addEventListener("touchmove", this.drag, false);
    this.container.addEventListener("touchend", this.dragEnd, false);

    this.container.addEventListener("mousedown", this.dragStart, false);
    this.container.addEventListener("mousemove", this.drag, false);
    this.container.addEventListener("mouseup", this.dragEnd, false);
  },
  methods: {
    dragStart(e) {
      if (e.type === "touchstart") {
        this.initialX = e.touches[0].clientX - this.xOffset;
        this.initialY = e.touches[0].clientY - this.yOffset;
      } else {
        this.initialX = e.clientX - this.xOffset;
        this.initialY = e.clientY - this.yOffset;
      }

      if (e.target === this.dragItem) {
        this.active = true;
      }
    },
    dragEnd() {
      this.initialX = this.currentX;
      this.initialY = this.currentY;
      this.active = false;
    },
    drag(e) {
      if (this.active) {
        e.preventDefault();

        if (e.type === "touchmove") {
          this.currentX = e.touches[0].clientX - this.initialX;
          this.currentY = e.touches[0].clientY - this.initialY;
        } else {
          this.currentX = e.clientX - this.initialX;
          this.currentY = e.clientY - this.initialY;
        }

        this.xOffset = this.currentX;
        this.yOffset = this.currentY;

        this.setTranslate(this.currentX, this.currentY, this.dragItem);
      }
    },
    setTranslate(xPos, yPos, el) {
      el.style.transform = "translate3d(" + xPos + "px, " + yPos + "px, 0)";
    },
    close() {
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

#calc {
  width: 235px;
  touch-action: none;
}

#item {
  width: 200px;
  height: 300px;
  touch-action: none;
  background-color: rgba(21, 21, 29, 0.5);
}
</style>
