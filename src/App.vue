<template>
  Component:
  <DayPlanner />
</template>

<script>
import DayPlanner from "./components/DayPlanner.vue";

export default {
  name: "App",
  components: {
    DayPlanner,
  },
};
</script>

<style>
#app {
  font-family: Roboto, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
