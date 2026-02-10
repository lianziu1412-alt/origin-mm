<template>
  <div>
    <HeaderComponent />
    <NuxtPage />
  </div>
</template>

<script setup>
import HeaderComponent from "@/components/HeaderComponent.vue";
</script>
<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
body {
  font-family: "Arial", sans-serif;
}
</style>
