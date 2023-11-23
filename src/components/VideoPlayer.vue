<template>
  <div>
    <div @click="player ? closePlayer() : openPlayer()">{{ player ? "关闭播放器" : "打开播放器" }}</div>
    <div v-if="player" style="width:100%;height:99.6vh;display: flex;justify-content: center;align-items: center;"
      id="playerTemp">
    </div>
  </div>
</template>

<script>
import zionMdapi from "zion-mdapi";
// import "../assets/aliplayer-h5-min.js";
import "//g.alicdn.com/apsara-media-box/imp-web-player/2.16.3/aliplayer-h5-min.js";
import "../assets/aliplayercomponents-1.0.9.min.js";
export default {
  name: "VideoPlayer",
  props: ["vid", "playauth", "course_log_pk", "url", "actionflow_id", "globalData"],
  data() {
    return {
      player: null
    }
  },
  mounted() {
    console.log("globalData:", this.globalData);
    console.log("vid:", this.vid);
    console.log("playauth:", this.playauth);
    console.log("course_log_pk:", this.course_log_pk);
    // this.createPlayer()
  },
  methods: {
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

      let props = {
        "id": "player-con",
        language: "zh-cn",
        cover: '',
        vid: this.vid || "a175d6b01b4271ee80980764b3ec0102",
        playauth: this.playauth || "eyJTZWN1cml0eVRva2VuIjoiQ0FJU2h3TjFxNkZ0NUIyeWZTaklyNWJEZnZMdzFZdGsxSmZjVEJEbHJERWlSUFphclpQNWlUejJJSGhKZVhOdkJPMGV0ZjQrbVdCWTdQY1lsck1xRjhjZUdSS1VNSlVodDgwTG9WUC9KcExGc3QySjZyOEpqc1ZjbE5naTNFZXBzdlhKYXNEVkVmbDJFNVhFTWlJUi8wMGU2TC8rY2lyWXBUWEhWYlNDbFo5Z2FQa09Rd0M4ZGtBb0xkeEtKd3hrMnQxNFVtWFdPYVNDUHdMU2htUEJMVXhtdldnR2wyUnp1NHV5M3ZPZDVoZlpwMXI4eE80YXhlTDBQb1AyVjgxbExacGxlc3FwM0k0U2M3YmFnaFpVNGdscjhxbHg3c3BCNVN5Vmt0eVdHVWhKL3phTElvaXQ3TnBqZmlCMGVvUUFQb3BGcC9YNmp2QWF3UExVbTliWXhncGhCOFIrWGo3RFpZYXV4N0d6ZW9XVE84MCthS3p3TmxuVXo5bUxMZU9WaVE0L1ptOEJQdzQ0RUxoSWFGMElVRUJ6RUc2Q2QvWDRvZ21hT2xmeUZaTG9pdjltamNCSHFIeno1c2VQS2xTMVJMR1U3RDBWSUpkVWJUbHpiME5MaHpPOEwvZGRLVjBSSXdNOVZ1eVBNYXgzYlFGRHI1M3ZzVGJiWHpaYjBtcHR1UG56ZHp0ZlB4elo3Q09WR29BQmFzQW5zMmNGZ1hkVURTWHFpdXFidTBkVmRpK0Q5UThsVS9PQ0Z2eVprT2lCODFwWFV2UVcxSStXMk51N0ZNVFVSaUM3dDVLM2Z2TkxqeHM0N1diM2VaMTAxTDdqZnE0dlZjajRyUFI1bHM5R2pjb1M1ZERGQTZLeG96emNaRlY2YTc4bkd6NFVVeGNJd0ZiNXRNSUVOa1hJcWRiUWd5b2V4aW9IS0ZuS2VBb2dBQT09IiwiQXV0aEluZm8iOiJ7XCJDSVwiOlwiaVJlZTAwMnNaSkxFSWZSV1RUQmcySW42c3pwRHRXZ3M1N0JVMkF6T3J3YlBpU0dtenlnRzEzblN6M3hHblovQVwiLFwiQ2FsbGVyXCI6XCJpNTJmbFZ6eUczOWNlNVhlSWF4RkVPSnB0MldEY1JPZGZSZHhScDJod2FvPVwiLFwiRXhwaXJlVGltZVwiOlwiMjAyMy0xMS0yM1QwNzowNjo1NVpcIixcIk1lZGlhSWRcIjpcImExNzVkNmIwMWI0MjcxZWU4MDk4MDc2NGIzZWMwMTAyXCIsXCJQbGF5RG9tYWluXCI6XCJzaGlwaW4uY2R4eWhweC5jb21cIixcIlNpZ25hdHVyZVwiOlwicDRvNVI0WmNBSWlXMStvb05md0dCcmdNcmEwPVwifSIsIlZpZGVvTWV0YSI6eyJTdGF0dXMiOiJOb3JtYWwiLCJWaWRlb0lkIjoiYTE3NWQ2YjAxYjQyNzFlZTgwOTgwNzY0YjNlYzAxMDIiLCJUaXRsZSI6IjEx44CB56ys5Y2B5LiA56ugIOebuOWFs+azleW+i+OAgeazleinhOefpeivhiIsIkNvdmVyVVJMIjoiaHR0cDovL3NoaXBpbi5jZHh5aHB4LmNvbS9hMTc1ZDZiMDFiNDI3MWVlODA5ODA3NjRiM2VjMDEwMi9zbmFwc2hvdHMvOTdmOTEwM2YxOTlhNGM3NjhhNGRiYzkwZDc0NDIzYmItMDAwMDQuanBnIiwiRHVyYXRpb24iOjQ1My40NH0sIkFjY2Vzc0tleUlkIjoiU1RTLk5VdjVIRDhURWNVN042VEgxd0h5dkJWUmsiLCJQbGF5RG9tYWluIjoic2hpcGluLmNkeHlocHguY29tIiwiQWNjZXNzS2V5U2VjcmV0IjoiQ2dXOEQzODZNWTduR2U2VjFSVDhaVmdYa3JDMXpkQ1NDMmh4QjJEdTd1UmIiLCJSZWdpb24iOiJjbi1zaGFuZ2hhaSIsIkN1c3RvbWVySWQiOjE0MDM0Njg1NjkxMTQ4ODZ9",
        //source: "//shipin.cdxyhpx.com/sv/3bba3a21-18926843f74/3bba3a21-18926843f74.mp4",
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
@import "//g.alicdn.com/apsara-media-box/imp-web-player/2.16.3/skins/default/aliplayer-min.css";
/* @import "../assets/aliplayer-min.css"; */

.prism-player {
  overflow: hidden;
}

.prism-info-display {
  box-sizing: border-box;
}
</style>
