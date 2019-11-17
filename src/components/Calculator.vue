<template>
  <div class="calc-wrapper">
    <Display :value="curr" />
    <div class="buttons">
      <Button :value="clearButtonValue" @click="onClear" />
      <Button
        v-for="button in buttons"
        :class="{ 'wide-button': button == '0' }"
        :key="button"
        :value="button"
        @click="onClick"
      />
    </div>
  </div>
</template>

<script>
import Display from "./Display";
import Button from "./Button";

export default {
  components: {
    Display,
    Button
  },
  data() {
    return {
      prev: "0",
      curr: "0",
      op: "+",
      justCleared: true,
      buttons: [
        "n",
        "p",
        "d",
        "7",
        "8",
        "9",
        "x",
        "4",
        "5",
        "6",
        "-",
        "1",
        "2",
        "3",
        "+",
        "0",
        ".",
        "="
      ]
    };
  },
  computed: {
    clearButtonValue() {
      return this.justCleared ? "ac" : "c";
    }
  },
  methods: {
    onClear(val) {
      if (val == "c") {
        this.curr = "0";
        this.justCleared = true;
      } else if (val == "ac") {
        this.prev = "0";
        // this.curr = "0"; // should already be the case, so no need to do it
        this.op = "+";
      }
    },
    onClick(val) {
      console.log("click", val);
      this.justCleared = false;
      switch (val) {
        case "n":
          if (this.curr != "0") {
            if (this.curr[0] != "-") this.curr = "-" + this.curr;
            else this.curr = this.curr.slice(1);
          }
          break;
        default:
          // numbers
          if (this.curr == "0") this.curr = val;
          else this.curr += val;
          break;
      }
    }
  }
};
</script>

<style scoped>
.calc-wrapper {
  background-color: darkslategrey;
}
.buttons {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-template-rows: repeat(4, 1fr);
  grid-gap: 1px;
}
.wide-button {
  grid-column: 1 / span 2;
}
</style>
