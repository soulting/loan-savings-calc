<script setup>
import HomeView from "./views/HomeView.vue";
</script>

<template>
  <HomeView />
</template>

<style>
#app {
  margin: 0;
  padding: 0;
}

body {
  background-color: white;
  padding: 0;
  margin: 15px;
}

@media (min-width: 1000px) {
  #app {
    display: flex;
    align-items: start;
    justify-content: center;
  }

  body {
    display: flex;
    align-items: start;
    justify-content: center;
  }
}
</style>
