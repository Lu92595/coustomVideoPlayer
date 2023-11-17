<template>
  <div>
    <video ref="videoPlayer" controls @loadedmetadata="loadedmetadata" @timeupdate="updateProgress">
      <source :src="videoSource" type="video/mp4">
      Your browser does not support the video tag.
    </video>
    <div>
      <p>当前播放时间: {{ currentTime }} 秒</p>
      <input type="range" v-model="currentTime" :max="duration" @input="seekTo">
    </div>
  </div>
</template>

<script>
export default {
  props: ['globalData'],
  data() {
    return {
      videoSource: '/video.mp4',
      currentTime: 30, // 设置默认进度为30秒
      duration: 0,
    };
  },
  methods: {
    updateProgress() {
      this.currentTime = this.$refs.videoPlayer.currentTime;
    },
    seekTo() {
      this.$refs.videoPlayer.currentTime = this.currentTime;
    },
    // 监听视频加载完成事件
    loadedmetadata() {
      console.log("globalData:", this.globalData);
      this.$refs.videoPlayer.currentTime = 12;
    }
  },
  mounted() {

  },
};
</script>



