<template>
  <div
    class="button-wrapper"
    :class="[colour, size, { border }]"
    @click="onClick"
  >
    <span>{{ symbol }}</span>
  </div>
</template>

<script>
export default {
  props: {
    symbol: {
      Type: String,
      required: true
    },
    colour: {
      type: String,
      default: "grey"
    },
    size: {
      type: String,
      default: "normal"
    },
    border: {
      type: Boolean,
      default: false
    }
  },
  methods: {
    onClick() {
      this.$emit("click", this.symbol);
    }
  }
};
</script>

<style scoped>
.button-wrapper {
  text-align: center;
  color: whitesmoke;
  height: 47px;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
  justify-content: center;
}
.border {
  border: 1px solid #333539;
  z-index: 10;
}
.orange {
  background-color: #f9a72c;
}
.orange:active {
  background-color: #c68627;
}
.grey {
  background-color: #69676f;
}
.grey:active {
  background-color: #a5a2b0;
}
.dark-grey {
  background-color: #47474d;
}
.dark-grey:active {
  background-color: #69676f;
}

.button-wrapper:hover {
  cursor: default;
}
.small {
  font-size: 18px;
}
.normal {
  font-size: 24px;
}
.large {
  font-size: 28px;
}
</style>
