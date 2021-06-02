<template>
  <div id="app">
    <chart></chart>
    <line-graph></line-graph>
  </div>
</template>

<script>
import Chart from './components/Chart.vue';
import LineGraph from './components/LineGraph.vue';

export default {
  name: 'App',
  components: {
    Chart,
    LineGraph,
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
