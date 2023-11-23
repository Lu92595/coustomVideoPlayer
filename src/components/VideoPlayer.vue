<template>
  <div>
    <div @click="createPlayer">{{ player ? "" : title }}</div>
    <div style="width:100%;height:99.6vh;display: flex;justify-content: center;align-items: center;" id="playerTemp">
    </div>
  </div>
</template>

<script>
import zionMdapi from "zion-mdapi";
// import "../assets/aliplayer-h5-min.js";
import "https://g.alicdn.com/apsara-media-box/imp-web-player/2.16.3/aliplayer-h5-min.js";

import "../assets/aliplayercomponents-1.0.9.min.js";
// import { onMounted, ref } from "vue";
// const props = defineProps({
//   video_source: {
//     type: String,
//     default: ""
//   },
//   url: {
//     type: String,
//     default: ""
//   },
//   actionflow_id: {
//     type: String,
//     default: ""
//   },
//   course_pk: {
//     type: Number,
//     default: 0
//   }
// })

// let title = ref("视频播放器")
// let player = ref(null);
// let playerTemp = ref(null)

// function createPlayer(source, cover) {
//   if (document.getElementById('player-con') === null) {
//     let playerDomWrap = playerTemp?.value;
//     let playerDom = document.createElement('div');
//     playerDom.setAttribute('id', 'player-con');
//     playerDomWrap.appendChild(playerDom);
//   }

//   let videoProps = {
//     "id": "player-con",
//     source: props?.video_source || "//player.alicdn.com/video/aliyunmedia.mp4",
//     // "width": "100%",
//     "height": "100vh",
//     "autoplay": false,
//     "isLive": false,
//     "rePlay": false,
//     "playsinline": true,
//     "preload": true,
//     "controlBarVisibility": "hover",
//     "useH5Prism": true
//   }
//   if (!player.value) {
//     player.value = new Aliplayer(videoProps);
//   }
// }

// onMounted(() => {
//   console.log(props.video_source);
// })



export default {
  name: "VideoPlayer",
  props: ["video_source", "url", "actionflow_id", "course_pk"],
  data() {
    return {
      player: null,
      title: "视频播放器"
    }
  },
  mounted() {
    console.log("video_source：", this.video_source);
    // this.createPlayer()
  },
  methods: {
    createPlayer(source, cover) {
      if (document.getElementById('player-con') === null) {
        let playerDomWrap = document.getElementById("playerTemp");
        let playerDom = document.createElement('div');
        playerDom.setAttribute('id', 'player-con');
        playerDomWrap.appendChild(playerDom);
      }

      let props = {
        "id": "player-con",
        language: "zh-cn",
        cover: '',
        source: "http://shipin.cdxyhpx.com/sv/3bba3a21-18926843f74/3bba3a21-18926843f74.mp4",
        // "width": "100%",
        "height": "100%",
        textTracks: [{ kind: 'subtitles', label: '英文（美国）', src: '字幕地址', srclang: 'en-US' }],
        "autoplay": false,
        "rePlay": false,
        "playsinline": true,
        "preload": true,
        "controlBarVisibility": "hover",
        "useH5Prism": true,
        components: [
          {
            name: 'BulletScreenComponent',
            type: AliPlayerComponent.BulletScreenComponent,
            args: ['欢迎使用阿里播放器', { fontSize: '16px', color: '#00c1de' }, 'random']
          },
          {
            name: 'CaptionComponent',
            type: AliPlayerComponent.CaptionComponent
          }]
      }
      if (!this.player) {
        this.player = new Aliplayer(props);
      }

      //挂载事件
      this.onReady();
    },
    onReady() {
      this.player.on('ready', (res) => {
        console.log("ready");
        this.player.seek(80)
        this.player.play()
      });
    }
  }
}


// const props = defineProps<{
//   globalData: Record<string, any>;
//   setGlobalData: (newValue: any) => void;
//   video_source?: string;
//   url?: string;
//   actionflow_id?: string;
//   course_pk: number;
// }>();
// const mdapi = zionMdapi.init({
//   url: props?.url,
//   env: "H5",
//   actionflow_id: props?.actionflow_id,
// })
// const videoPlayer = ref<any>(null);
// const videoSource = ref<string>(props.video_source || "");
// const currentTime = ref<number>(0);

// function preventDrag(event: any) {
//   event.preventDefault();
// }

// // 开始播放
// function toPlay() {
//   if (videoPlayer.value) {
//     videoPlayer.value.play();
//   }
//   console.log("播放");
// }

// // 暂停
// function toPause() {
//   if (videoPlayer.value) {
//     videoPlayer.value.pause();
//   }
//   console.log("暂停");
// }

// // 播放结束
// function onEnded() {
//   console.log("播放结束了");
//   mdapi.callActionflow({
//     actionflow_name: "视频组件_播放结束",
//     payload: {
//       course_pk: props?.course_pk
//     }
//   })
// }

// // 播放进度
// function onTimeupdate() {
//   currentTime.value = videoPlayer.value.currentTime;
//   console.log("播放进度上传中");
// }

// // 准备好了
// function onReady() {
//   videoPlayer.value.currentTime = 20;
//   console.log("video准备就绪");
// }


// onMounted(() => {

//   let player = new Aliplayer({
//     "id": "player-con",
//     source: "//player.alicdn.com/video/aliyunmedia.mp4",
//     // "width": "100%",
//     // "height": "500px",
//     "autoplay": false,
//     "isLive": false,
//     "rePlay": false,
//     "playsinline": true,
//     "preload": true,
//     "controlBarVisibility": "hover",
//     "useH5Prism": true
//   }, function (player) {

//   })


//   document.addEventListener("visibilitychange", (e: any) => {
//     // if (e.target.visibilityState == "visible") {
//     //   toPlay()
//     // } else {
//     //   toPause()
//     // }
//   });
// });

// onUnmounted(() => {
//   //document.removeEventListener("visibilitychange", () => { })
// });
</script>
<style>
@import "https://g.alicdn.com/apsara-media-box/imp-web-player/2.16.3/skins/default/aliplayer-min.css";
/* @import "../assets/aliplayer-min.css"; */

.prism-player {
  overflow: hidden;
}

.prism-info-display {
  box-sizing: border-box;
}
</style>
