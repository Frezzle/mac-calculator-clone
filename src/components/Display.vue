<template>
  <div class="display-wrapper">
    {{ value }}
  </div>
</template>

<script>
export default {
  props: ["value"]
};
</script>

<style scoped>
.display-wrapper {
  background-color: darkslategrey;
  color: whitesmoke;
  font-size: 42px;
  text-align: right;
}
</style>
