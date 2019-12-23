<template>
  <div class="calc-wrapper" id="drag-item">
    <span class="circles">
      <span class="circle red" @click="close">
        <span class="circle-symbol">×</span>
      </span>
      <span class="circle yellow" @click="minimize">
        <span class="circle-symbol">-</span>
      </span>
      <span class="circle green" @click="expand">
        <span class="circle-symbol">+</span>
      </span>
    </span>
    <Display :value="display" class="display" :class="`d-${uuid}`" />
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
      <div>uuid = {{ uuid }}</div>
      <br />
      <div>Window data:</div>
      <div>dragContainer = {{ dragContainer }}</div>
      <div>dragItem = {{ dragItem }}</div>
      <div>displayItem = {{ displayItem }}</div>
      <div>dragActive = {{ dragActive }}</div>
      <div>currentX = {{ currentX }}</div>
      <div>currentY = {{ currentY }}</div>
      <div>initialX = {{ initialX }}</div>
      <div>initialY = {{ initialY }}</div>
      <div>xOffset = {{ xOffset }}</div>
      <div>yOffset = {{ yOffset }}</div>
      <br />
      <div>Calculator data:</div>
      <div>display = {{ display }}</div>
      <div>memory = {{ memory }}</div>
      <div>modifier = {{ modifier }}</div>
      <div>op = {{ op }}</div>
      <div>activeOp = {{ activeOp }}</div>
      <div>justCleared = {{ justCleared }}</div>
      <div>justPressedOp = {{ justPressedOp }}</div>
      <div>justEqualed = {{ justEqualed }}</div>
    </div>
    <div class="window-inset-glow" />
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
    uuid: {
      default: 0
    },
    debug: {
      type: Boolean,
      default: false
    },
    initialDisplay: {
      type: String,
      default: "0"
    },
    initialWindowPosition: {
      type: Object,
      default: () => {
        return {
          x: 0,
          y: 0
        };
      }
    }
  },
  data() {
    return {
      // draggable window data
      dragContainer: null,
      dragItem: null,
      displayItem: null,
      dragActive: false,
      currentX: null,
      currentY: null,
      initialX: null,
      initialY: null,
      xOffset: 0,
      yOffset: 0,
      // calc data
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
  mounted() {
    this.dragContainer = document.getElementsByTagName("body")[0];
    this.dragItem = this.$el;
    this.displayItem = this.$el.getElementsByClassName("display")[0];

    // TODO use props to either set initial position relative to screen (top/left css props) or relative to parent component (useful for undocking scenario).
    this.setTranslate(
      this.initialWindowPosition.x,
      this.initialWindowPosition.y
    );

    this.dragContainer.addEventListener("touchstart", this.dragStart, false);
    this.dragContainer.addEventListener("touchmove", this.drag, false);
    this.dragContainer.addEventListener("touchend", this.dragEnd, false);

    this.dragContainer.addEventListener("mousedown", this.dragStart, false);
    this.dragContainer.addEventListener("mousemove", this.drag, false);
    this.dragContainer.addEventListener("mouseup", this.dragEnd, false);
  },
  beforeDestroy() {
    this.dragContainer.removeEventListener("touchstart", this.dragStart, false);
    this.dragContainer.removeEventListener("touchmove", this.drag, false);
    this.dragContainer.removeEventListener("touchend", this.dragEnd, false);

    this.dragContainer.removeEventListener("mousedown", this.dragStart, false);
    this.dragContainer.removeEventListener("mousemove", this.drag, false);
    this.dragContainer.removeEventListener("mouseup", this.dragEnd, false);
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
    },
    dragStart(e) {
      if (e.target === this.dragItem || e.target === this.displayItem) {
        if (e.type === "touchstart") {
          this.initialX = e.touches[0].clientX - this.xOffset;
          this.initialY = e.touches[0].clientY - this.yOffset;
        } else {
          this.initialX = e.clientX - this.xOffset;
          this.initialY = e.clientY - this.yOffset;
        }

        this.dragActive = true;
      }
    },
    dragEnd() {
      this.initialX = this.currentX;
      this.initialY = this.currentY;
      this.dragActive = false;
    },
    drag(e) {
      if (this.dragActive) {
        e.preventDefault();

        if (e.type === "touchmove") {
          this.currentX = e.touches[0].clientX - this.initialX;
          this.currentY = e.touches[0].clientY - this.initialY;
        } else {
          this.currentX = e.clientX - this.initialX;
          this.currentY = e.clientY - this.initialY;
        }

        this.setTranslate(this.currentX, this.currentY);
      }
    },
    setTranslate(xPos, yPos) {
      this.xOffset = xPos;
      this.yOffset = yPos;
      this.dragItem.style.transform =
        "translate3d(" + xPos + "px, " + yPos + "px, 0)";
    }
  }
};
</script>

<style scoped>
.calc-wrapper {
  background-color: #333539;
  border-radius: 6px;
  /* border: 1px solid #5d6067; */
  border: 1px solid rgba(0, 0, 0, 1);
  overflow: hidden;
  user-select: none;
  box-shadow: 0 22px 40px 4px rgba(0, 0, 0, 0.56);
  width: 235px;
  box-sizing: border-box;
  touch-action: none;
  position: fixed;
}
.window-inset-glow {
  position: absolute;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
  pointer-events: none;
  border-radius: 5px; /* less than the wrapper's border-radius, to copy visuals from OSX calc */
  box-shadow: inset 0 1px #9c9fa78e, inset 0 -1px #85888f8e,
    inset 1px 0 #85888f8e, inset -1px 0 #85888f8e; /* top is a bit brighter */
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
  border-radius: 50%;
  display: inline-flex;
  justify-content: center;
  align-items: center;
  margin-top: 5px; /* fix why this needs to be so big; should only need to be 1px */
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
#drag-item {
  touch-action: none; /* needed? */
}
</style>
