<script setup lang="ts">
import Display from "./components/Display.vue";
</script>

<template>
  <Display value="Hello Zion" :global-data="{name:'444'}" :set-global-data="() => {}" />
</template>

<style scoped>
</style>
