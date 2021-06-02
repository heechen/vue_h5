<template>
 <transition name="move"> 
  <div class="mysignlefood" v-show="showFlag" ref="mysignlefoodref">
    <div class="food-content">
      <div class="image-header">
        <img :src="food.image">
        <div class="back" @click="hide">
            <i class="icon-arrow_lift"></i>
        </div>
        
      </div>
      <div class="content">
        <h1 class="title">{{food.name}}</h1>
        <div class="detail">
          <span class="seller-count">月售{{food.sellCount}}</span>
          <span class="rating">好评率{{food.rating}}%</span>
        </div>
        <div class="price">
          <span class="new">￥{{ food.price }}</span>
          <span class="old" v-show="food.oldPrice">￥{{ food.oldPrice }}</span>
        </div>
        
        <div class="cartcontrol-wrapper">
          <cartcontrol :food = food @my-cartadd="myCartAdd"></cartcontrol>
        </div>
        <transition name="mysignlefoodfade"> 
        <div @click.stop.prevent="addFrist" class="buy" v-show="!food.count || food.count===0">加入购物车</div>
        </transition>

      </div> 
      
      <div class="info" v-show="food.info">
        <split></split>
        <h1 class="title">商品信息</h1>
        <p class="text">{{food.info}}</p>
      </div>
      <split></split>
      <div class="rating">
        <h1 class="title">商品评价</h1>
        <ratingselect :selectType="selectType" :onlyContent="onlyContent" :desc="desc" :ratings="food.ratings" @ratingtype-select="ratingtypeSelect" @content-toggle="contentToggle"></ratingselect>
        <div class="rating-wrapper">
          <ul v-show="food.ratings">
  
             <li v-show="needShow(rating.rateType,rating.text)"  v-for="rating in food.ratings" class="rating-itme border-1px">
               <div class="user">
                 <span class="name">{{rating.username}}</span>
                 <img class="avatar" width="12px" height="12px" :src ="rating.avatar" ></img>
               </div>
               <div class="time">{{rating.rateTime | formatDate}}</div>
               <p class="text">
                 <span :class="{'icon-thumb_up':rating.rateType===0,'icon-thumb_down':rating.rateType===1}"></span>{{rating.text}}
               </p>
             </li>
          </ul>
          <div class="norating" v-show="food.ratings && !food.ratings.length">暂无评价</div>
        </div>
      </div>
    </div>
  </div>
  </transition> 
</template>

<script>
import Vue from 'vue'
import BScroll from 'better-scroll';
import {formatDate} from '@/commom/js/date.js';
import cartcontrol from "@/components/cartcontrol/cartcontrol";
import split from "@/components/split/split";
import ratingselect from "@/components/ratingselect/ratingselect";
const POSITIVE = 0
const NEGATIVE=1
const ALL =2
export default {
  components:{cartcontrol,split,ratingselect},
  
  props:{
    food:{
      type:Object
    }
  },
  filters:{
    formatDate(time){
      let date = new Date(time)
      return formatDate(date,'yyyy-MM-dd hh:mm:ss')
    }
  },
  data() {
    return {
      showFlag:false,
      selectType:ALL,
      onlyContent:false,
      desc:{
        all:'全部',
        positive:'推荐',
        negative:'吐槽',
      }
       
    };
  },
  created(){
     
  },
  methods:{
    show(){
      this.showFlag = true
      this.selectType=ALL
      this.onlyContent=false

      this.$nextTick(()=>{
        if(!this.scroll){
          this.scroll = new BScroll(this.$refs.mysignlefoodref,{
            click:true
          })
        }else{
          this.scroll.refresh()
        }
      })
    },
    hide(){
      this.showFlag = false
    },
    addFrist(event){
      if (!event._constructed) { //浏览器直接return掉,去掉自带click事件的点击
        return;
      }
      Vue.set(this.food,'count',1)
      this.$emit('my-cartadd',event.target)
    },
    myCartAdd(el){
      this.$emit('my-cartadd',el)
    },
    needShow(type,text){
      if(this.onlyContent && !text){
        return false
      }
      if (this.selectType === ALL) {
        return true
      }else{
        return type === this.selectType
      }
    },
    ratingtypeSelect(type){
      this.selectType = type
      this.$nextTick(()=>{
        this.scroll.refresh()
      })
      
    },
    contentToggle(val){
      this.onlyContent = val
      this.scroll.refresh()
      
    }
  }
};
</script>

<style lang="stylus">
@import '../../commom/stylus/minx.styl';
.mysignlefood
  position fixed
  left 0
  bottom 48px
  z-index 30
  width 100%
  height 100%
  background #fff
  transition all .2s
  transform translate3d(0,0,0)
  &.move-enter,&.move-leave-to
   transform translate3d(100%,0,0)
  .image-header
    position relative
    width 100%
    height 0
    padding-top 100%
    img
      position absolute
      top 0
      left 0
      width 100%
      height 100%
    .back
      position absolute
      top 50px
      left 0
      .icon-arrow_lift
        display block
        padding 10px
        font-size 12px
        color #fff
  .content
    position relative
    padding 18px
    .title
      line-height 14px
      margin-bottom 8px
      font-size 14px
      font-weight 700
      color rgb(7,17,27)
    .detail
      margin-bottom 18px
      line-height 10px
      height 10px
      font-size 0
      .seller-count,.rating
        font-size 10px
        color rgb(147,153,159)
      .seller-count
        padding-right 12px
    .price 
      font-weight: 700;
      line-height: 24px;
      .new
        margin-right: 8px
        font-size: 14px
        color: rgb(240, 20, 20)
      .old 
        text-decoration: line-through;
        font-size: 10px;
        color: rgb(147, 153, 159);
  .cartcontrol-wrapper
    position absolute
    right 12px
    bottom 12px
  .buy
    position absolute
    right 18px
    bottom  18px
    z-index 10
    height 24px
    line-height 24px
    padding 0 12px
    box-sizing border-box
    border-radius 12px
    font-size 10px
    color #fff
    background rgb(0,160,220) 
    &.mysignlefoodfad-enter-active,&.mysignlefoodfad-leave-active
      transition all 0.2s
      opacity 1
    &.mysignlefoodfade-enter,&.mysignlefoodfade-leave-active
      opacity 0
  .info
    padding 18px
    .title
      line-height 14px
      margin-bottom 6px
      font-size 14px
      font-weight 700
      color rgb(7,17,27)
    .text
      line-height 18px
      padding 0 8px
      font-size 12px
      color rgb(77,85,93)
  .rating
    padding-top 18px
    .title
      line-height 14px
      margin-left  18px
      font-size 14px
      font-weight 700
      color rgb(7,17,27)
    .rating-wrapper
      padding 0 18px
      .rating-itme
        position relative
        padding 16px 0
        border-1px(rgba(7,17,27,0.1))
        .user
          position absolute
          right 0
          top 16px
          line-height 12px
          font-size 0
          .name
            display inline-block
            margin-right 6px
            vertical-align top
            font-size 10px
            color rgb(147,153,159)
          .avatar
            border-radius 50%
        .time
          margin-bottom 6px
          line-height 12px
          font-size 10px
          color rgb(147,153,159)
        .text
          line-height 16px
          font-size 12px
          color rgb(7,17,27)
          .icon-thumb_up,.icon-thumb_down
            margin 0 4px 0
            line-height 16px
            font-size 12px
          .icon-thumb_up
            color rgb(0,160,220)
          .icon-thumb_down
            color rgb(147,153,159)
      .norating
        padding 16px 0
        font-size 12px
        color rgb(147,153,159)

</style>