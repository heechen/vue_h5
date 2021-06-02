<template>
  <div class="ratingselect">
      <div class="rating-type border-1px">
        <span @click="select(2,$event)" class="block positive" :class="{'active':selectTypeData===2}">{{desc.all}}<span class="count">{{ratings.length}}</span></span>
        <span @click="select(0,$event)" class="block positive" :class="{'active':selectTypeData===0}">{{desc.positive}}<span class="count">{{positiveComputed}}</span></span>
        <span @click="select(1,$event)" class="block negative" :class="{'active':selectTypeData===1}">{{desc.negative}}<span class="count">{{negativeComputed}}</span></span>
      </div>
      <div @click="toggleContent" class="switch" :class="{'on':onlyContentData}">
        <span class="icon-check_circle"></span>
        <span class="text">只看有内容的评价</span>
      </div>
  </div>
</template>

<script>
import BScroll from 'better-scroll'
const POSITIVE = 0
const NEGATIVE=1
const ALL =2
export default {
    props:{
      ratings:{
        type:Array,
        default:function () {
          return [];
        }
      },
      selectType:{
        type :Number,
        defalut:ALL
      },
      onlyContent:{
        type :Boolean,
        default:false
      },
      desc:{
        type :Object,
        default:function () {
          return {
            all:'全部',
            positive:'满意',
            negative:'不满意',
            
          }
        }
      }
    },
  
    data(){
        return {
           selectTypeData: this.selectType,
           onlyContentData:this.onlyContent
        }
    },
    //难点:子组件不可以修改props的值,此时需要一个data数据并用watch来监听data数据的变化，从而达到数据双向绑定
    watch:{
      selectType:function (val) {
        this.selectTypeData =val
      },
      onlyContentData:function (val) {
        this.onlyContentData =val
      }
    },
 
    computed:{
      positiveComputed(){
        return this.ratings.filter((rating)=>{
          return rating.rateType===POSITIVE
        }).length
      },
      negativeComputed(){
        return this.ratings.filter((rating)=>{
          return rating.rateType===NEGATIVE
        }).length
      },
    },
    methods: {
      select(type,event){
        if(!event._constructed){
          return
        }
        this.selectTypeData=type
        this.$emit('ratingtype-select',this.selectTypeData)
        
      },
       toggleContent(event){
       if(!event._constructed){
          return
        }
        this.onlyContentData = !this.onlyContentData
        this.$emit('content-toggle',this.onlyContentData)
    }
    },
   
    
}
</script>

<style lang="stylus">
@import '../../commom/stylus/minx.styl'
.ratingselect 
  .rating-type
    padding 18px 0
    margin 0 18px
    border-1px(rgba(7,17,27,0.1))
    font-size 0
    .block
      display inline-block
      padding 8px 12px
      margin-right 8px
      border-radius 1px
      font-size 12px
      color rgb(77,85,93)
      &.active
        color #fff
      .count
        margin-left 2px
        font-size 8px
      &.positive
        background rgba(0,160,220,0.2)
        &.active
          background rgb(0,160,220)
      &.negative
        background rgba(77,85,93,0.2)
        &.active
          background rgb(77,85,93)
  .switch
    padding 12px 18px
    line-height 24px
    border-bottom 1px solid rgba(7,17,27,0.1)
    color rgb(147,153,159)
    &.on
      .icon-check_circle
        color #00c850
    .icon-check_circle
      display inline-block
      vertical-align top
      margin-right 4px
      font-size 24px
    .text
      display inline-block
      vertical-align top
      font-size 12px


      


      



</style>
