<template>
  <div id="app">
    <country />
  </div>
</template>

<script>
import country from "./components/Products";
export default {
  name: "app",
  components: { country }
};
</script>
<style lang="scss">
@import "./assets/all";
</style>
