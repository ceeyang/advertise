<template>
  <div>
    <audio controls ref="myAudio" hidden>
      <source :src="getAudioPath()" type="audio/mpeg">
      您的浏览器不支持
    </audio>
    <swiper-img :style="{opacity: showSwiper?1:0}" v-if="loadSwiper" @play-audio="playAudio" @pause-audio="pauseAudio"></swiper-img>
    <transition name="light">
      <div class="loading" v-show="!showSwiper">
        <div class="text" v-show="showText">
          <transition-group name="fade">
            <p :key="1" v-show="showOne">你将看到一组特殊的影视作品</p >
            <p :key="2" v-show="showTwo">它们来自一群「特别」的作者</p >
          </transition-group>
        </div>
        <div class="gif-img" v-show="showText"></div>
      </div>
    </transition>
  </div>
</template>
<script>
  import SwiperImg from './SwiperImg.vue'
  export default {
    components: {
      SwiperImg
    },
    data() {
      return {
        showOne: false,
        showTwo: false,
        showSwiper: false,
        loadSwiper: false,
        showText: true,
        audioIdx: 0,
        isPlayAudio: false,
        audioList: ['turn_on_light', '01_循环_冰面', '02_循环_宇宙', '03_循环_海浪', '04_循环_岩石']
      }
    },
    methods: {
      getAudioPath() {
        return require('../assets/audio/' + this.audioList[this.audioIdx] + '.mp3')
      },
      playAudio(idx) {
        // 切换视频，先关掉之前的
        this.$refs.myAudio.pause()
        this.isPlayAudio = true
        setTimeout(()=>{
          this.audioIdx = idx
          this.$refs.myAudio.src = this.getAudioPath()
          this.$refs.myAudio.loop = 'loop'
        }, 500)
      },
      pauseAudio() {
        this.isPlayAudio = false
        this.$refs.myAudio.pause()
      },
      jump2Next() {
        this.loadSwiper = true
        setTimeout(() => {
          console.log('开始加载组件----')
          this.$nextTick(function () {
            console.log('加载完毕----')
            this.showText = false
            setTimeout(()=>{
              this.showSwiper = true
              // 播放音效
              this.$refs.myAudio.play()
              setTimeout(()=>{
                this.playAudio(1)
              }, 3000)
            }, 1000)
          })
//          this.$router.push({path: '/swiper-img'})
        }, 4000)
      },
      addText(timer=0) {
        this.loadSwiper = true
        setTimeout(() => {
          if (!this.showOne) {
            this.showOne = true
            this.addText(3000)
          } else if (!this.showTwo) {
            this.showTwo = true
            this.jump2Next()
          }
        }, timer)
      },
    },
    mounted() {
      this.$nextTick(function () {
//        this.addText()
        document.addEventListener('touchmove', function(e) {
          e.preventDefault();
        }, false);
        let _this = this
        this.$refs.myAudio.addEventListener('canplay', function () {
          console.log('音频准备就绪')
          if (_this.isPlayAudio) {
            this.play()
          }
        }, false)
      })
    },
    created() {
      window.onload = this.addText
    }
  }
</script>
<style lang="scss">
  @import "style/common";
  .light-leave-active {
    transition: opacity 3s;
  }
  .light-enter{
    opacity: 1;
  }
  .light-leave-to {
    opacity: 0;
  }
  .loading {
    background: url("../assets/ic_loading_bg.png") no-repeat center;
    background-size: cover;
    width: 100vw;
    height: 100vh;
    position: fixed;
    left: 0;
    top: 0;
    .gif-img{
      position: absolute;
      bottom: 10vh;
      left: 50%;
      transform: translateX(-50%);
      background: url("../assets/Loading.gif") no-repeat center;
      background-size: 100%;
      width: 14vw;
      height: 3vh;
    }
    .text{
      top: 40%;
    }
	}
</style>
