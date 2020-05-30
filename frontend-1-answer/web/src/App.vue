<template>
  <div id="app">
    <CategoryPane />
    <BillPane />
  </div>
</template>

<script>
import BillPane from "./components/BillPane.vue";
import CategoryPane from "./components/CategoryPane";
export default {
  name: "App",
  components: {
    BillPane,
    CategoryPane
  },
  methods: {
    
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
