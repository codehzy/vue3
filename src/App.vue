<template>
  <div id="app">
    <h1>{{ count }}</h1>
    <h1>{{ double }}</h1>
  </div>
  <button @click="increase">👍🏻+1</button>
</template>

<script lang="ts">
import { ref, computed } from "vue";
export default {
  name: "App",
  setup() {
    const count = ref(0);
    const double = computed(() => {
      return count.value * 2;
    });
    const increase = () => {
      count.value++;
    };
    return {
      count,
      increase,
      double,
    };
  },
};
</script>

<style></style>
