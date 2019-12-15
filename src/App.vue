<template>
  <div id="app">
    <Calculator class="calc" />
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
*/

export default {
  name: "app",
  components: {
    Calculator
  }
};
</script>

<style>
#app {
  font-family: Helvetica, Arial, sans-serif;
  margin: 30px;
}

html,
body {
  background-color: rgb(34, 34, 34);
}

.calc {
  width: 235px;
}
</style>
