<template>
  <div>
    <video ref="videoPlayer" width="600" controls @timeupdate="updateProgress">
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

console.log(this);
export default {
  props: ['globalData'],
  data() {
    return {
      videoSource: 'your_video_path.mp4',
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
  },
  mounted() {
    console.log(this.globalData);
    // 监听视频加载完成事件
    this.$refs.videoPlayer.addEventListener('loadedmetadata', () => {
      this.duration = this.$refs.videoPlayer.duration;
      this.$refs.videoPlayer.currentTime = this.currentTime; // 设置默认进度
    });
  },
};
</script>



