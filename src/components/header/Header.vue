<template>
  <div class="header">
    <div class="content-wrapper">
      <div class="avatar">
        <img width="64" height="64" :src="seller.avatar" alt="">
      </div>
      <div class="content">
        <div class="title">
          <span class="brand"></span>
          <span class="name">{{seller.name}}</span>
        </div>
        <div class="description">
          {{seller.description}}/{{seller.deliveryTime}}
        </div>
        <div v-if="seller.supports" class="supports">
          <span class="icon" :class="classMap[seller.supports[0].type]"></span>
          <span class="text">{{seller.supports[0].description}}</span>
        </div>
      
      </div>
      <div v-if = "seller.supports" class="support-count" @click="showDetail">
        <span class="count">{{seller.supports.length}}个</span>
        <i class="icon-keyboard_arrow_right"></i>
      </div>
    
    </div>
    <div class="bulletin-wapper" @click="showDetail">
        <span class="bulletin-title"></span><span class="bulletin-text">{{seller.bulletin}}</span>
        <i class="icon-keyboard_arrow_right"></i>
    </div>
    <div class="background">
      <img :src="seller.avatar" alt="" width="100%" height="100%">
    </div>
    <!-- sticky footer 技巧套路 + vue动画-->
    <transition name="fade">
    <div v-show="detailShow" class="detail">
       <div class="detail-wrapper clearfix">
         <div class="detail-main">
           <div class="name">{{seller.name}}</div> 
           <div class="star-wrapper">
             <star :size="36" :score="seller.score"></star> 
           </div>
           <vline :linetext="seller.title1"></vline>
           <div class="shoper-support">
             <ul v-if="seller.supports" class="supports">
               <li class="support-item" v-for="(item,index) in seller.supports">
                 <span class="icon" :class="classMap[seller.supports[index].type]"></span>
                 <span class="text">{{seller.supports[index].description}}</span>
               </li>
             </ul>
           </div>
           <vline :linetext="seller.title2"></vline>
           <div class="shoper-tips">
             {{seller.bulletin}}
           </div>
           
         </div>
       </div>
       <div class="detail-close" @click="closeDetail">
        <i class="icon-close"></i>
        </div>        
    </div>
    </transition>
    
  </div>
</template>

<script>
import Star from '@/components/star/star'
import Vline from '@/components/line/line'
export default {
 props:{
   seller:{
     type:Object
   }
 },
 data(){
   return {
     detailShow:false
   }
 },
 created(){
   this.classMap = ['decrease','discount','special','invoice','guarantee']
 },
 components:{
  Star,
  Vline
 },
 methods:{
   showDetail(){
     this.detailShow = true
   },
   closeDetail(){
     this.detailShow = false
   }
 }

}
</script>

<style lang="stylus">
@import '../../commom/stylus/minx.styl';
    .header
      position :relative
      overflow: hidden
      color :#fff
      background :rgba(7,17,27,0.3)
      .content-wrapper
        position :relative
        padding:24px 12px 18px 24px
        font-size:0px 
        .avatar
          display :inline-block
          vertical-align :top
          img
            border-radius: 5px
        .content
          display :inline-block
          margin-left :16px
          
          .title
            margin 2px 0 8px 0
            .brand
              display inline-block
              vertical-align :top
              width :30px
              height :18px
              bg-image('brand')
              background-size :30px 18px
            .name
              margin-left :6px
              font-size :16px
              line-height 16px
              font-weight:bold
          .description
            margin-bottom :10px
            font-size :12px
            line-height 12px
          .supports
            .icon
              display :inline-block
              vertical-align :top
              width :12px
              height :12px
              margin-right :4px
              background-size:12px 12px
              background-repeat:no-repeat
              &.decrease
                bg-image('decrease_1')
              &.discount
                bg-image('discount_1')
              &.guarantee
                bg-image('guarantee_1')
              &.invoice
                bg-image('invoice_1')
              &.special
                bg-image('special_1')
            .text
              line-height:12px
              font-size :10px
        .support-count
          position :absolute
          right :12px
          bottom :18px
          padding :0 8px
          height :24px
          line-height :24px
          background : rgba(0,0,0,0.3)
          text-align :center
          border-radius:10px
          .count
            vertical-align :top
            font-size :10px
          .icon-keyboard_arrow_right
            margin-left :2px
            font-size :10px
            line-height:24px
      .bulletin-wapper
        position :relative
        height:28px
        line-height :28px
        padding : 0 22px 0 12px
        white-space :nowrap
        overflow :hidden
        text-overflow: ellipsis
        background :rgba(1,17,27,0.2)
        .bulletin-title
          display: inline-block
          vertical-align :top
          margin-top :8px
          width :22px
          height :12px
          bg-image('bulletin')
          background-size :22px,12px
          background-repeat:no-repeat
        .bulletin-text
          vertical-align :top
          margin:0 4px
          font-size :10px
        .icon-keyboard_arrow_right
          position :absolute
          font-size :10px
          top:8px
          right :12px
      .background
        position : absolute
        top :0
        left:0
        width : 100%
        height : 100%
        z-index : -1
        filter :blur(10px)
      .detail
        position :fixed
        overflow :auto
        top :0
        left :0
        width :100%
        height :100%
        z-index : 100
        background :rgba(7,17,27,0.6)
        backdrop-filter :blur(10px)
        &.fade-enter, &.fade-leave-to
          opacity :0
        &.fade-enter-active, &.fade-leave-active
          transition: opacity .5s;
        .detail-wrapper
          min-height :100%
          width :100%
          .detail-main
            margin-top : 64px
            padding-bottom:64px
            .name
              line-height :16px
              text-align :center
              font-size :16px
              font-weight 700
            .star-wrapper
              text-align :center
              margin-top:18px
              padding :2px 0
            .shoper-support
              .supports
                width :80%
                margin :0 auto
                .support-item
                  padding :0 12px
                  margin-bottom :12px
                  font-size ：0
                  &:last-child
                    margin-bottom :0
                  .icon
                   display:inline-block
                   width :16px
                   height :16px
                   vertical-align :top
                   margin-right :6px
                   background-size :16px 16px
                   background-repeat :no-repeat
                   &.decrease
                     bg-image('decrease_2')
                   &.discount
                     bg-image('discount_2')
                   &.guarantee
                     bg-image('guarantee_2')
                   &.invoice
                     bg-image('invoice_2')
                   &.special
                     bg-image('special_2')
                  .text
                    line-height :12px
                    font-size:12px
            .shoper-tips
              width: 80%;
              margin: 0 auto;
              line-height: 20px;
              font-size: 12px;   
        
                

        .detail-close
          position :relative
          width :32px
          height :32px
          margin :-64px auto 0 auto
          clear :both
          font-size :32px



      
    

          
</style>
