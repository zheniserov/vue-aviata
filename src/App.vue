<template>
  <div id="app">
    <SearchResult />
  </div>
</template>

<script>
import SearchResult from "@/components/SearchResult";
export default {
  name: "App",
  components: {
    SearchResult
  }
};
</script>

<style>
#app {
  font-family: Open Sans, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  background: #D7D7D7;
}
</style>
