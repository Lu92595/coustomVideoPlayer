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

      // 当前播放时间
      currentTime: 0,

      // 上次触发更新时间戳
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
  methods: {
    async getLastUpdateTime() {
      const { data, msg, status } = await this.mdapi.callActionflow({
        actionflow_name: "获取视频进度",
        payload: {
          course_log_pk: this.course_log_pk
        }
      })
      if (status !== "成功") {
        throw new Error(msg)
      }
      const { maxCurrentTime = 0 } = data || {};
      this.maxCurrentTime = maxCurrentTime;
    },
    async postMaxCurrentTime() {
      const { data, msg, status } = await this.mdapi.callActionflow({
        actionflow_name: "更新视频进度",
        payload: {
          course_log_pk: this.course_log_pk,
          maxCurrentTime: this.maxCurrentTime
        }
      })
    },

    initMadpi() {
      this.mdapi = zionMdapi.init({
        url: this.url,
        actionflow_id: this.actionflow_id,
        env: "H5"
      })
    },
    closePlayer() {
      this.player = null;
    },
    openPlayer() {
      this.player = true;
      this.$nextTick().then(() => {
        this.createPlayer()
      })
    },
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
      }

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
