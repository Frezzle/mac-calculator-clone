<template>
  <div class="display-wrapper" :style="`font-size:${fontSize}px`">
    {{ value }}
  </div>
</template>

<script>
export default {
  props: ["value"],
  computed: {
    fontSize() {
      return (
        46 - (Math.max(this.value.length, 6) - 6) * (3 - this.value.length / 20)
      );
    }
  }
};
</script>

<style scoped>
.display-wrapper {
  color: white;
  text-align: right;
  font-weight: 100;
  padding: 5px 17px;
  height: 65px;
  box-sizing: border-box;
  display: flex;
  justify-content: right;
  align-items: flex-end;
  line-height: 100%;
}
</style>
