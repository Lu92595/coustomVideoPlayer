<template>
  <div style="height: 100%;display: flex;justify-content: center;align-items: center;flex-direction: column;">
    <!-- <div @click="player ? closePlayer() : openPlayer()">{{ player ? "关闭播放器" : "打开播放器" }}</div> -->
    <div v-if="player" style="width:100%;flex:1;display: flex;justify-content: center;align-items: center;"
      id="playerTemp">
    </div>
  </div>
</template>
<script>
import zionMdapi from "zion-mdapi";
// import "../assets/aliplayer-h5-min.js";
import "https://g.alicdn.com/apsara-media-box/imp-web-player/2.16.3/aliplayer-h5-min.js";
import "../assets/aliplayercomponents-1.0.9.min.js";
export default {
  name: "VideoPlayer",
  props: ["globalData", "source", "vid", "playauth", "course_log_pk", "cover", "bullet_screen", "url", "actionflow_id"],
  data() {
    return {
      player: null,
      mdapi: null,

      // 上次观看进度更新时间
      lastWatchTime: 0,

      // 当前播放进度
      currentTime: 0,

      // 上次触发更新视频进度时间
      lastUpdateTime: 0,
      // 最大播放进度
      maxCurrentTime: 0,

    }
  },
  mounted() {
    console.log("props:", this.$props);

    this.initMadpi();
    this.openPlayer();
  },
  onUnmounted() {
    if (this.player) {
      this.player.dispose();
    }
  },
  methods: {
    // 获取视频进度
    async getMaxCurrentTime() {
      const { data, msg, status } = await this.mdapi.callActionflow({
        actionflow_name: "获取视频进度",
        payload: {
          course_log_pk: this.course_log_pk
        }
      })
      if (status !== "成功") {
        console.error(msg, data)
        //throw new Error(msg)
      }
      this.maxCurrentTime = data?.max_current_time || 0;
      return this.maxCurrentTime;
    },
    // 视频进度更新
    async postMaxCurrentTime() {
      this.currentTime = this.player.getCurrentTime();

      // 播放进度超过最大进度时
      if (this.currentTime > this.maxCurrentTime && this.player.getStatus() == "playing") {
        const now_time = new Date().getTime();
        // 每秒钟更新下本地的最大进度
        if (this.lastUpdateTime && now_time - this.lastWatchTime > 1000 * 1) {
          this.maxCurrentTime = this.currentTime;
          this.lastWatchTime = now_time;
        }
        // 每30秒推送一次播放进度到后台
        if (now_time - this.lastUpdateTime < 1000 * 30) {
          return
        }
        this.maxCurrentTime = this.currentTime;
        this.lastUpdateTime = now_time;
        const { data, msg, status } = await this.mdapi.callActionflow({
          actionflow_name: "更新视频进度",
          payload: {
            course_log_pk: this.course_log_pk,
            maxCurrentTime: this.maxCurrentTime,
            duration: this.player.getDuration()
          }
        })
        if (status !== "成功") {
          console.error(msg, data)
        }

      }
    },
    // 初始化mdapi
    initMadpi() {
      this.mdapi = zionMdapi.init({
        url: this.url,
        actionflow_id: this.actionflow_id,
        env: "H5"
      })
    },
    // 关闭播放器
    closePlayer() {
      this.player.dispose();
      this.player = null;
    },
    // 打开播放器
    openPlayer() {
      this.player = true;
      this.$nextTick(() => {
        this.createPlayer()
      })
    },
    // 创建播放器
    createPlayer() {
      if (document.getElementById('player-con') === null) {
        let playerDomWrap = document.getElementById("playerTemp");
        let playerDom = document.createElement('div');
        playerDom.setAttribute('id', 'player-con');
        playerDomWrap.appendChild(playerDom);
      }

      const props = {
        id: "player-con",
        language: "zh-cn",
        cover: this.cover || "",
        vid: this.vid || "",
        playauth: this.playauth || "",
        source: this.source || "",
        height: "100%",

        autoplay: false,
        rePlay: false,
        playsinline: true,
        preload: true,
        controlBarVisibility: "hover",
        useH5Prism: true,

        components: [
          {
            name: 'BulletScreenComponent',
            type: AliPlayerComponent.BulletScreenComponent,
            args: [this.bullet_screen || "", { fontSize: '16px', color: '#00c1de' }, 'random']
          }
        ]
      }
      if (!this.player || this.player === true) {
        this.player = new Aliplayer(props);

        //挂载事件
        this.onReady();
        this.onTimeupdate();
        this.onStartSeek();
        this.onCompleteSeek();
        this.onPlay();
        this.onPause();
      }

    },
    // 播放器准备就绪时
    onReady() {
      this.player.on('ready', async (res) => {
        const maxCurrentTime = await this.getMaxCurrentTime()
        this.player.seek(maxCurrentTime)
        this.player.play()
      });
    },
    //播放进度更新时
    onTimeupdate() {
      this.player.on('timeupdate', async (res) => {
        await this.postMaxCurrentTime();
      });
    },
    //开始播放时
    onPlay() {
      this.player.on('play', res => {
        console.log("play", res);
        const currentTime = this.player.getCurrentTime();
        if (currentTime > this.maxCurrentTime) {
          this.player.seek(this.maxCurrentTime)
        }
      });
    },
    // 播放到暂停时
    onPause() {
      this.player.on('pause', res => {
        console.log("pause", res);
        const currentTime = this.player.getCurrentTime();
        if (currentTime > this.maxCurrentTime) {
          this.player.seek(this.maxCurrentTime)
        }
      });
    },
    // 开始拖动进度条时
    onStartSeek() {
      this.player.on('startSeek', (res) => {
        console.log("startSeek", res);
        this.player.pause()
      });
    },
    // 进度条拖动完成时
    onCompleteSeek() {
      this.player.on('completeSeek', res => {
        console.log("completeSeek", res);
        const timer = setTimeout(() => {
          clearTimeout(timer)
          this.player.play()
        }, 300)

      });
    }
  }
}
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
