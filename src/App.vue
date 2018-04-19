<template>
  <div id="app">
    <section class="header"></section>
    <section class="clock" v-bind:class="[!ifEndNum?'clock-pre':'']">
      <div class="timer">
        <span>{{ifEndNum?"距今日秒抢仅剩":"今日秒抢已结束"}}</span>
        <Timer :endTime="1524042999000" v-on:ifEnd="ifEnd"></Timer>
      </div>
      <AwardList :ifEndNum="ifEndNum"></AwardList>     
    </section>
    <section class="bottol">
      <div class="ball-gamer-pos">
        <BallGamer></BallGamer>
      </div>
      <div class="award-pos">
        <AwardList :ifEndNum="1"></AwardList>    
      </div>
    </section>
    <section class="listen"></section>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld'
import Timer from './components/Timer'
import AwardList from './components/AwardList'
import BallGamer from './components/BallGamer'

export default {
  name: 'App',
  components: {
    HelloWorld,
    Timer,
    AwardList,
    BallGamer
  },
  data(){
    return {
      ifEndNum: 0  //倒计时判断
    }
  },
  mounted(){
    // console.info('process:',process.env.NODE_ENV)    
    
    let v    
    (async ()=>{
      try {
        v = await new Promise(resolve => {
          setTimeout(() => resolve("long_time_value"), 2000);
        });
        // v = await this.axios.get('https://www.baidu.com')
        v += ' handled'
        console.log(v);
        
      } catch (err) {
        console.log(err);
      }
    })()
  },
  methods:{
    ifEnd: function(data){
      this.ifEndNum = parseInt(data)
      // console.log(this.ifEndNum);
    }
  }
}
</script>

<style lang='stylus' >
  @import 'assets/styl/common.styl';

  #app
    font-family 'Avenir', Helvetica, Arial, sans-serif
    -webkit-font-smoothing antialiased
    -moz-osx-font-smoothing grayscale
    text-align center
    color #2c3e50
  section
    background-size 100%
    background-repeat no-repeat
    &.header
      height 788px
      background-image url('./assets/img/index01.jpg')
    &.clock
      position relative
      height 1181px
      background-image url('./assets/img/index02_2.jpg')
      font-size 34px
      color #fff
      font-weight 700
    &.clock-pre
      height 906px
      background-image url('./assets/img/index02.jpg')
    &.bottol
      position relative
      height 2050px
      background-image url('./assets/img/index03.jpg')
      .ball-gamer-pos
        position absolute
        top 300px
        left 0
        right 0
        height 990px
      .award-pos
        position absolute
        width 620px
        height 144px
        margin-left -370px
        padding 18px 50px
        left 50%
        bottom 320px
        overflow hidden
    &.listen
      height 1512px
      background-image url('./assets/img/index04.jpg')
  .timer
    position absolute
    display flex
    align-items center
    top 75px
    left 50%
    width 740px
    margin-left -370px
    span
      display block
      width 290px
      padding 22px 0
      font-size 32px
      text-align center
</style>
