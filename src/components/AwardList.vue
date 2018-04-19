<template>
  <ul class="award-list" :class="[!ifEndNum?'hide':'']">
    <li><span>139****1212</span><i>获得华为watch2</i></li>
    <li><span>139****1212</span><i>获得华为watch2</i></li>
    <li><span>139****1212</span><i>获得华为watch2</i></li>
  </ul>
</template>
<script>
export default {
  name: "AwardList",
  props: {
    ifEndNum:{
      type:Number
    }
  },
  data(){
    return {
    }
  },
  mounted(){
    
  },
  methods: {

  }
}
</script>
<style lang="stylus" scoped>
  .hide
    display none
  .award-list
    width 100%
    height 100%
    overflow-y auto
    &&::-webkit-scrollbar
      display none
    li
      display flex
      justify-content space-between
      line-height 55px
      color #fff
</style>


