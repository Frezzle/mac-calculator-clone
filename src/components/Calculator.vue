<template>
  <div class="calc-wrapper">
    <span class="circles">
      <span class="circle red" @click="close"
        ><span class="circle-symbol">×</span></span
      >
      <span class="circle yellow" @click="minimize">
        <span class="circle-symbol">&minus;</span>
      </span>
      <span class="circle green" @click="expand"
        ><span class="circle-symbol">+</span></span
      >
    </span>
    <Display :value="display" />
    <div class="buttons">
      <Button
        :symbol="clearButtonValue"
        colour="dark-grey"
        size="small"
        @click="onClear"
      />
      <Button
        v-for="button in buttons"
        :class="{ 'wide-button': button.symbol == '0' }"
        :key="button.symbol"
        :symbol="button.symbol"
        :colour="button.colour"
        :size="button.size"
        :border="button.symbol == activeOp"
        @click="onClick"
      />
    </div>
    <div v-if="debug" class="debug">
      <div>display = {{ display }}</div>
      <div>memory = {{ memory }}</div>
      <div>modifier = {{ modifier }}</div>
      <div>op = {{ op }}</div>
      <div>activeOp = {{ activeOp }}</div>
      <div>justCleared = {{ justCleared }}</div>
      <div>justPressedOp = {{ justPressedOp }}</div>
      <div>justEqualed = {{ justEqualed }}</div>
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
  props: {
    debug: {
      type: Boolean,
      default: false
    },
    initialDisplay: {
      type: String,
      default: "0"
    }
  },
  data() {
    return {
      memory: 0,
      modifier: 0,
      display: this.initialDisplay,
      op: null,
      activeOp: null,
      justCleared: true,
      justPressedOp: false,
      justEqualed: false,
      buttons: [
        {
          symbol: "±",
          colour: "dark-grey"
        },
        {
          symbol: "%",
          colour: "dark-grey",
          size: "small"
        },
        {
          symbol: "÷",
          colour: "orange"
        },
        {
          symbol: "7"
        },
        {
          symbol: "8"
        },
        {
          symbol: "9"
        },
        {
          symbol: "×",
          colour: "orange"
        },
        {
          symbol: "4"
        },
        {
          symbol: "5"
        },
        {
          symbol: "6"
        },
        {
          symbol: "-",
          colour: "orange"
        },
        {
          symbol: "1"
        },
        {
          symbol: "2"
        },
        {
          symbol: "3"
        },
        {
          symbol: "+",
          colour: "orange"
        },
        {
          symbol: "0"
        },
        {
          symbol: "."
        },
        {
          symbol: "=",
          colour: "orange"
        }
      ]
    };
  },
  computed: {
    clearButtonValue() {
      return this.justCleared ? "AC" : "C";
    }
  },
  methods: {
    onClear(val) {
      this.justPressedOp = false;
      this.justEqualed = false;
      if (val == "C") {
        this.display = "0";
        this.modifier = 0;
        this.justCleared = true;
      } else if (val == "AC") {
        this.memory = 0;
        this.op = null;
        this.activeOp = null;
      }
    },
    onClick(val) {
      this.justCleared = false;

      switch (val) {
        case "±":
          this.display = -Number(this.display) + "";
          break;
        case "%":
          this.display = this.display / 100 + "";
          break;
        case "÷":
        case "×":
        case "+":
        case "-":
          this.justPressedOp = true;
          this.activeOp = val;
          if (this.op && !this.justEqualed) {
            const res = this.operate(this.memory, this.display);
            this.memory = res;
            this.display = res + "";
          } else {
            this.memory = Number(this.display);
          }
          this.op = val;
          break;
        case "=": {
          if (!this.justEqualed) {
            this.modifier = Number(this.display);
          }
          const res = this.operate(this.memory, this.modifier);
          this.memory = res;
          this.display = res + "";

          this.justEqualed = true;
          this.activeOp = null;
          break;
        }
        case ".":
          if (this.display == "0") this.display = "0.";
          else if (!this.display.includes(".")) this.display += ".";
          break;
        default:
          // numbers
          if (this.display == "0" || this.justPressedOp) this.display = val;
          else this.display += val;
          break;
      }

      switch (val) {
        case "÷":
        case "×":
        case "+":
        case "-":
          break;
        default:
          this.justPressedOp = false;
      }

      if (val != "=") this.justEqualed = false;
    },
    operate(x, y) {
      switch (this.op) {
        case "÷":
          return Number(x) / Number(y);
        case "×":
          return Number(x) * Number(y);
        case "+":
          return Number(x) + Number(y);
        case "-":
          return Number(x) - Number(y);
        default:
          return Number(y);
      }
    },
    close() {
      this.$emit("close");
    },
    minimize() {
      this.$emit("minimize");
    },
    expand() {
      this.$emit("expand");
    }
  }
};
</script>

<style scoped>
.calc-wrapper {
  background-color: #333539;
  border-radius: 8px;
  overflow: hidden;
  user-select: none;
  box-shadow: rgba(0.2, 0.2, 0.2, 0.4) 0px 0px 25px 15px;
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
.debug {
  color: white;
  padding: 10px;
}
.circles {
  margin-left: 8px;
}
.circle {
  width: 12px;
  height: 12px;
  border-radius: 20px;
  display: inline-flex;
  justify-content: center;
  align-items: center;
}
.circle:not(:first-of-type) {
  margin-left: 8px;
}
.red {
  background-color: rgb(252, 93, 65);
}
.red:hover:active {
  background-color: rgb(186, 68, 47);
}
.yellow {
  background-color: rgb(222, 210, 88);
}
.yellow:hover:active {
  background-color: rgb(164, 156, 68);
}
.green {
  background-color: rgb(82, 186, 82);
}
.green:hover:active {
  background-color: rgb(60, 133, 60);
}
.circle-symbol {
  font-size: 11px;
  font-weight: 600;
  visibility: hidden;
}
.circles:hover .circle-symbol {
  visibility: visible;
}
</style>
