<template>
  <div class="what-calls" @click="getMousePos">
    <div class="score">
      <span class="text"
        >得&nbsp;分&nbsp;:&nbsp;&nbsp;&nbsp;&nbsp;{{ score }}</span
      >
    </div>
    <el-image
      :style="TheStyle"
      :src="require('@/assets/Cow.jpg')"
      fit="cover"
      id="img"
      @click="findhim"
    ></el-image>
    <audio src="@/assets/牛叫.mp3" ref="mp3"></audio>
  </div>
</template>

<script>
export default {
  name: "what-calls",
  data() {
    return {
      TheStyle: {
        position: "relative",
        top: "0vh",
        left: "0vw",
        width: "20vh",
        height: "20vh",
        opacity: "0",
      },
      //屏幕大小px
      clientX: 0,
      clientY: 0,
      //点击位置px
      clickX: 0,
      clickY: 0,
      //图片位置px
      imgX: 0,
      imgY: 0,
      //点击距离图片距离百分比
      long: 0,
      //图片距离四个顶点的距离
      long_left_top: 0,
      long_left_bottom: 0,
      long_right_top: 0,
      long_right_bottom: 0,
      //图片到四个顶点的最长距离
      maxlong: 0,
      //得分
      score: 0,
      //previous
      previous: 0,
    };
  },
  methods: {
    getMousePos(event) {
      let e = event;
      this.clickX = e.clientX;
      this.clickY = e.clientY;
      this.long = Math.sqrt(
        Math.pow(this.clickX - this.imgX, 2) +
          Math.pow(this.clickY - this.imgY, 2)
      );
      let mp3 = this.$refs.mp3;
      if(1 - this.long / this.maxlong < 0.4){
        mp3.volume = 0.1
      }else if(1 - this.long / this.maxlong < 0.6){
        mp3.volume = 0.3
      }else if(1 - this.long / this.maxlong < 0.8){
        mp3.volume = 0.6
      }else if(1 - this.long / this.maxlong < 1){
        mp3.volume = 0.9
      }
      console.log(mp3.volume);
      if (mp3.paused) {
        mp3.play();
      } else {
        mp3.currentTime = 0;
        mp3.play();
      }
    },
    findhim() {
      //节流
      const now = Date.now();
      if (now - this.previous > 2000) {
        this.TheStyle.opacity = "1";
        this.score += 1;
        this.previous = now;
        setTimeout(() => {
          this.initialize();
          this.TheStyle.opacity = "0";
        }, 2000);
      }
    },
    initialize() {
      //计算屏幕宽高
      this.clientX = document.body.clientWidth;
      this.clientY = document.body.clientHeight;
      //随机生成图片位置
      let left = Math.round(Math.random() * 80 + 0);
      let top = Math.round(Math.random() * 55 + 0);
      this.TheStyle.top = top.toString() + "vh";
      this.TheStyle.left = left.toString() + "vw";
      //计算图片随机位置
      this.imgX = (left * this.clientX) / 100;
      this.imgY = (top * this.clientY) / 100;
      //计算图片至四个顶点的距离
      this.long_left_top = Math.sqrt(
        Math.pow(this.imgX, 2) + Math.pow(this.imgY, 2)
      );
      this.long_left_bottom = Math.sqrt(
        Math.pow(this.imgX, 2) + Math.pow(this.clientY - this.imgY, 2)
      );
      this.long_right_top = Math.sqrt(
        Math.pow(this.clientX - this.imgX, 2) + Math.pow(this.imgY, 2)
      );
      this.long_right_bottom = Math.sqrt(
        Math.pow(this.clientX - this.imgX, 2) +
          Math.pow(this.clientY - this.imgY, 2)
      );
      this.maxlong = Math.max(
        this.long_right_top,
        this.long_right_bottom,
        this.long_left_top,
        this.long_left_bottom
      );
    },
  },
  mounted() {
    this.initialize();
    this.$alert(
      "游戏规则: 找到隐藏在大草原中的牛,点击屏幕,牛会发出叫声,点击的距离距离牛越近,牛的叫声会越大,找到藏在草原中的牛吧！",
      "欢迎来到what-calls",
      {
        confirmButtonText: "试听一下(最大声牛叫!)",
      }
    ).then(() => {
      let mp3 = this.$refs.mp3;
      mp3.play();
    });
  },
};
</script>

<style lang='less' scoped>
.what-calls {
  width: 100vw;
  height: 100vh;
  background-color: green;
  .score {
    position: relative;
    top: 2vh;
    left: 2vh;
    border-radius: 20px;
    width: 10vw;
    height: 8vh;
    background-color: white;
    z-index: 0;
    text-align: center;
    font-size: 1em;
    .text {
      line-height: 8vh;
    }
  }
}
</style>