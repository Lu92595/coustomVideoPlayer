<template>
  <div>
    <div class="container">
      <h1>this is a Vue3 Component</h1>
      <div>value from outer globalData.value: {{ value || "no input" }}</div>
      <input @input="onInput" />
    </div>

    <video ref="videoPlayer" width="600" controls @timeupdate="updateProgress" @loadedmetadata="loadedmetadata">
      <source :src="videoSource" type="video/mp4">
      Your browser does not support the video tag.
    </video>

    <div>
      <p>当前播放时间: {{ currentTime }} 秒</p>
      <input type="range" v-model="currentTime" :max="duration" @input="seekTo">
    </div>

  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue';
const props = defineProps<{
  globalData: Record<string, any>;
  setGlobalData: (newValue: any) => void;
  value?: string;
}>();
const videoPlayer = ref<HTMLVideoElement | null>(null);
const videoSource = '/video.mp4';
const currentTime = ref<number>(0);
const duration = ref<number>(0);

const onInput = (e: Event) => {
  props.setGlobalData({
    ...props.globalData,
    value: (e.target as HTMLInputElement)?.value,
  });
}
const updateProgress = () => {
  if (videoPlayer.value) {
    currentTime.value = videoPlayer.value.currentTime;
  }
};

const seekTo = () => {
  if (videoPlayer.value) {
    videoPlayer.value.currentTime = currentTime.value;

  }
};

const loadedmetadata = () => {
  if (videoPlayer.value) {
    videoPlayer.value.currentTime = 20;
  }
}

onMounted(() => {

});

onUnmounted(() => {
  if (videoPlayer.value) {
    videoPlayer.value.removeEventListener('loadedmetadata', () => { });
  }
});
</script>
