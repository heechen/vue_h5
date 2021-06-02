<template>
  <div class="cartcontrol">
    <transition name="cartmove">
         <div class="cart-decrease" @click.stop="decreaseCart" v-show="food.count>0">
            <span class="inner icon-remove_circle_outline"></span>
         </div>
     </transition>
     <div class="cart-count" v-show="food.count>0">{{food.count}}</div>
     <div class="cart-add icon-add_circle" @click.stop="addCart"></div>
   
  </div>
</template>

<script>
import Vue from 'vue';
export default {
   props:{
      food:{
         type:Object,
      }
   },
    data(){
        return {
            
        }
    },
     methods:{
        addCart(event){ 
           if(!event._constructed){
            return;//去除pc
           }
           if(!this.food.count){
              Vue.set(this.food,'count',1)
           }else{
              this.food.count++
           }
           console.log("cartcontrol22");
           this.$emit('my-cartadd',event.target)
        },
        decreaseCart(event){
            if(!event._constructed){
            return;//去除pc
           }
           if(this.food.count){
            this.food.count--
           }
        }
     }
  }
</script>

<style lang="stylus">
   .cartcontrol
      font-size 0
      .cart-count
         display inline-block
         vertical-align top 
         width 12px
         padding-top 6px
         line-height 24px
         text-align center
         color :rgb(147,153,159)
         font-size 10px
         
      .cart-decrease
         display inline-block
         padding 6px
         transition all .5s linear
         &.cartmove-enter-to,&.cartmove-leave
          opacity :1
          transform :translate3d(0,0,0)
          .inner
           transform :rotate(0)
         &.cartmove-enter,&.cartmove-leave-to
          opacity :0
          transform :translate3d(20px,0,0)
          .inner
           transform :rotate(180deg)
         .inner
            display inline-block
            line-height 24px
            font-size 24px
            color rgb(0,160,220)
            transition all .5s linear
            
             
      .cart-add
         display inline-block
         padding 6px
         line-height 24px
         font-size 24px
         color rgb(0,160,220) 

      
</style>
