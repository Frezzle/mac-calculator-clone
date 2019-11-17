<template>
  <div class="button-wrapper" @click="onClick">
    {{ renderedValue }}
  </div>
</template>

<script>
export default {
  props: ["value"],
  computed: {
    renderedValue() {
      switch (this.value) {
        case "c":
          return "C";
        case "ac":
          return "AC";
        case "n":
          return "±";
        case "p":
          return "%";
        case "d":
          return "÷";
        default:
          return this.value;
      }
    }
  },
  methods: {
    onClick() {
      this.$emit("click", this.value);
    }
  }
};
</script>

<style scoped>
.button-wrapper {
  background-color: orange;
  color: white;
  font-size: 32px;
}
.button-wrapper:hover {
  cursor: default;
}
.button-wrapper:active {
  filter: saturate(30%);
}
</style>
