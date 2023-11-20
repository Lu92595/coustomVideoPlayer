<script setup lang="ts">
import Display from "./components/Display.vue";
import { reactive, ref } from "vue";

const globalData = reactive({ value: "http://shipin.cdxyhpx.com/sv/3bba3a21-18926843f74/3bba3a21-18926843f74.mp4" });
const setGlobalData = (obj: any) => {
  globalData.value = obj.value;
}

const url = ref("https://zion-app.functorz.com/mwRRZnqJQwn/zero/1qjzLgQDzzK/api/graphql-v2");
const actionflow_id = ref("39b8c22f-5d78-446b-a046-a2967986da5c")

</script>

<template>
  <Display :video_source="globalData?.value" :url="url" :actionflow_id="actionflow_id" :global-data="globalData"
    :set-global-data="setGlobalData" />
</template>

<style scoped></style>
