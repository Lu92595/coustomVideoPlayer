<template>
  <div style="width: 100%;padding: 0;margin: 0;">
    <video ref="videoPlayer" style="width: 100%;margin: 0;padding: 0;" controls @timeupdate="onTimeupdate"
      @loadedmetadata="onReady">
      <source style="width: 100%;margin: 0;padding: 0;" :src="videoSource" type="video/mp4">
      Your browser does not support the video tag.
    </video>
  </div>
</template>

<script setup lang="ts">
import zionMdapi from "zion-mdapi";
import { ref, onMounted, onUnmounted } from 'vue';

const props = defineProps<{
  globalData: Record<string, any>;
  setGlobalData: (newValue: any) => void;
  video_source?: string;
  url?: string;
  actionflow_id?: string;
}>();
const mdapi = zionMdapi.init({
  url: props?.url,
  env: "H5",
  actionflow_id: props?.actionflow_id,
})
const videoPlayer = ref<HTMLVideoElement | null>(null);
const videoSource = ref<string>(props.video_source || "");
const currentTime = ref<number>(0);


// 开始播放
function toPlay() {
  if (videoPlayer.value) {
    videoPlayer.value.play();
  }
  console.log("播放");
}

// 暂停
function toPause() {
  if (videoPlayer.value) {
    videoPlayer.value.pause();
  }
  console.log("暂停");
}

// 播放结束
function onEnded() {
  console.log("播放结束了");
}

// 播放进度
function onTimeupdate() {
  currentTime.value = videoPlayer.value.currentTime;
  console.log("播放进度上传中");
}

// 准备好了
function onReady() {
  videoPlayer.value.currentTime = 20;
  console.log("video准备就绪");
}


onMounted(() => {
  document.addEventListener("visibilitychange", (e: any) => {
    if (e.target.visibilityState == "visible") {
      toPlay()
    } else {
      toPause()
    }
  });
});

onUnmounted(() => {
  document.removeEventListener("visibilitychange", () => { })
});
</script>
