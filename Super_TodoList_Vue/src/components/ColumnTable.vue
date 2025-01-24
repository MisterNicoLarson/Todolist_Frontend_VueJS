<script setup lang="ts">
defineProps({
  h1_title: {
    type: String,
    required: true
  }
});
</script>

<template>
  <div class="container">
    <div class="title-column">
      <h1>{{ h1_title }}</h1>
    </div>
    <div class="tableau">
      <slot></slot>
    </div>
  </div>
</template>


<style>
:root {
  --full-width: 100%;
  --min-width: 400px;
  --full-height: 100%;
  --min-height: 400px;
  --font-size: 10px;
  --bg-color: aliceblue;
  --text-color: black;
  --fixed-height: 500px;
}

.container {
  display: grid;
  grid-template-rows: auto 1fr;
  margin: 0;
  padding: 0;
  height: var(--fixed-height);
}

.title-column {
  text-align: center;
  background-color: rgba(240, 248, 255, 0.8); /* aliceblue more darker */
  font-size: var(--font-size);
  width: var(--full-width);
  min-width: var(--min-width);
  color: var(--text-color);
  margin: 0;
  padding: 0;
}

.tableau {
  background-color: var(--bg-color);
  width: var(--full-width);
  min-width: var(--min-width);
  margin: 0;
  padding: 0;
  overflow-y: auto;
}
</style>