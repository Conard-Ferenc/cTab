<script setup lang="ts">
import SearchBar from '@components/SearchBar.vue'
</script>

<template>
  <div class="container">
    <header class="header">
      <SearchBar />
    </header>
    <svg width="20" height="20"><text>AA</text></svg>
  </div>
</template>

<style scoped>
.container {
  display: grid;
  grid-template-rows: 1fr 3fr 50px;
  box-sizing: border-box;
  padding: 20px;
  height: 100vh;
  width: 100vw;
  background-image: url(https://api.yimian.xyz/img?size=1920x1080);
  background-repeat: no-repeat;
  background-size: 15e2px;
  background-position: center;
}
.container::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  backdrop-filter: blur(0.7px) grayscale(0.2);
}
.container > * {
  z-index: 1;
}
</style>
