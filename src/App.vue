<script setup lang="ts">
import Display from "./components/Display.vue";
import { reactive } from "vue";

const globalData = reactive({ value: "love & peace" });
const setGlobalData = (obj: any) => {
  globalData.value = obj.value;
}
</script>

<template>
  <Display :value="globalData?.value" :global-data="globalData" :set-global-data="setGlobalData" />
</template>

<style scoped></style>
