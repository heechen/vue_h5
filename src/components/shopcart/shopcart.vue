<template>
  <div class="shopcart">
      <div class="content" @click="toggleList">
          <div class="content-left">
              <div class="logo-wrapper">
                  <div class="logo" :class="{'hightlight':totalCount>0}">
                      <i class="icon-shopping_cart" :class="{'hightlight':totalCount>0}"></i>
                  </div>
                  <div class="num" v-show="totalCount>0">{{totalCount}}</div>
              </div>

              <div class="price" :class="{'hightlight':totalCount>0}">￥{{totalPrice}}元</div>
              <div class="desc">另需配送费{{deliveryPrice}}元</div>
          </div>
          <div class="content-right">
            <div class="pay" :class="payClass">
              {{payDesc}}
            </div>
          </div>
      </div>
      <div class="ball-container"> 
	      <transition-group name="drop" v-on:before-enter="beforeEnter" v-on:enter="enter" v-on:after-enter="afterEnter">
	          <div class="ball" v-for="(ball,index) in balls" v-show="ball.show" :key="ball.id">
	      		  <div class="inner inner-hook"></div>
	      	  </div>
	      </transition-group>
      </div>
      <transition name="fold">
      <div class="shopcart-list" v-show="listShow">
        <div class="list-header">
          <h1 class="title">购物车</h1>
           <span class="empty" @click="emptySelectFood">清空</span>
        </div>
        <div class="list-content" ref="listcontentref">
          <ul>
            <li class="food" v-for="food in selectFoods">
              <span class="name">{{food.name}}</span>
              <div class="price">
                <span>￥{{food.price * food.count}}</span>
              </div>
              <div class="cartcontrol-wrapper">
                <cartcontrol :food=food></cartcontrol>
              </div>
            </li>
          </ul>
        </div>
      </div>
      </transition>
  </div>
</template>

<script>
import cartcontrol from "@/components/cartcontrol/cartcontrol";
import BScroll from 'better-scroll';
export default {
  components: {cartcontrol},
   props:{
     selectFoods:{
       type:Array,
        // default: () => [
        //   {price:20,count:3}
        // ]
        default:function () {
          return []
        }
        
     },
     deliveryPrice:{
       type:Number,
       defalut:0
     },
      minPrice:{
       type:Number,
       defalut:0
     }
   },
  data(){
     return {
       fold:true,
       balls:[{
         id:1,
         show:false
       },
       {
         id:2,
         show:false
       },
       {
         id:3,
         show:false
       },
       {
         id:4,
         show:false
       },
       {
         id:5,
         show:false
       }
       ],
        dropBalls: [] // 用dropBalls来存放掉落的小球
       
   }
  },
    computed:{
      totalPrice(){
        let total=0
        this.selectFoods.forEach(food => {
          total += food.price * food.count
        });
        return total
      },
      totalCount(){
        let total=0
        this.selectFoods.forEach(food => {
          total +=  food.count
        });
        return total
      },
      payDesc(){
        if(this.totalPrice === 0){
          return `￥${this.minPrice}元起送`
        }else if(this.totalPrice<this.minPrice){
          let diff =this.minPrice-this.totalPrice
          return `还差￥${diff}元起送`
        }else{
          return '去结算'
        }
      },
      payClass(){
        if(this.totalPrice<this.minPrice){
            return 'not-enough'
        }else{
            return 'enough'
        }
      },
      listShow(){
        if(!this.totalCount){
          this.fold = true
          return false
        }
        let show = !this.fold
        if(show){
          this.$nextTick(()=>{
            if (!this.scroll) {
              this.scroll =new BScroll(this.$refs.listcontentref,{
               probeType: 3,
               click:true
            })
            }else{
              this.scroll.refresh()
            }
            
          })
        }
        return show;
      }
    },
    mounted() {
     
    },
    methods: {
      drop(el) {
          //遍历小球，找到show为false的小球
          for(let i=0;i<this.balls.length;i++){
      	    let ball = this.balls[i];
      	    if(!ball.show) {
      		    ball.show = true;//显示
      		    ball.el = el;//用小球的el对象保留这个element,用来计算小球位置
      		    this.dropBalls.push(ball);//已经下落的小球
      		    return;
      		}  
      	}
      },

      beforeEnter: function (el) {//el为当前执行transition动画的dom对象
    /*el是小球;
	遍历所有show为true的小球*/
	let count = this.balls.length;
	while(count--) {
		let ball = this.balls[count];
		if(ball.show) {
			let rect = ball.el.getBoundingClientRect();//获得该元素（加号）相对于视口的位置的偏移（left,top）
			let x = rect.left-32;
			let y = -(window.innerHeight-rect.top-22);
			el.style.display = '';
			el.style.webkitTransform = `translate3d(0,${y}px,0)`;//外层做纵向运动
			el.style.transform = `translate3d(0,${y}px,0)`;
			let inner = el.getElementsByClassName('inner-hook')[0];
			inner.style.webkitTransform = `translate3d(${x}px,0,0)`;
			inner.style.transform = `translate3d(${x}px,0,0)`;
		}
	}
      },
      enter: function (el) {
	let rf = el.offsetHeight;//必须重绘，再进行transform才有用
	this.$nextTick(() => {
	    el.style.webkitTransform = 'translate3d(0,0,0)';//外层做纵向运动
		el.style.transform = 'translate3d(0,0,0)';
		let inner = el.getElementsByClassName('inner-hook')[0];
		inner.style.webkitTransform = 'translate3d(0,0,0)';
		inner.style.transform = 'translate3d(0,0,0)';
	});
      },
      afterEnter: function (el) {
        let ball = this.dropBalls.shift();
        if(ball) {
        	  ball.show = false;
            el.style.display = 'none';
        }


    
      },
      toggleList(){
        if (!this.totalCount) {
          return
        }
        this.fold = !this.fold
      },
      emptySelectFood(){
        //this.selectFoods = []
        this.$emit('empty',this.selectFoods)
    }
    },
  

}
</script>

<style lang="stylus">
.shopcart
  position fixed
  left:0
  bottom:0
  z-index 50
  width 100%
  height 48px
  //background #000
  .content
    display flex
    background #141d27
    font-size 0
   .content-left
     flex 1
    .logo-wrapper
      display inline-block
      position relative
      top -10px
      margin 0 12px
      padding 6px
      width 56px
      height 56px
      box-sizing border-box
      vertical-align: top
      border-radius 50%
      background #141d27
      .logo
        width :100%
        height 100%
        border-radius 50%
        text-align center
        background #2b343c
        &.hightlight
          background rgb(0,160,220)
        .icon-shopping_cart
            line-height 44px
            font-size 24px
            color  #80858a
            &.hightlight
             color #fff
      .num
        position absolute
        top 0
        right 0
        width 24px
        height 16px
        line-height 16px
        text-align center
        border-radius 16px
        font-size 9px
        font-weight 700
        color #fff
        background rgb(240,20,20)
        box-shadow 0 4px 8px 0 rgba(0,0,0,0.4)      
    .price
      display inline-block
      vertical-align top
      margin-top :12px
      line-height 24px 
      padding-right 12px
      box-sizing border-box
      border-right 1px solid rgba(255,255,255,0.2)
      font-size :16px
      font-weight 700
      color rgba(255,255,255,0.4)
      &.hightlight
        color #fff
    .desc
      display inline-block 
      vertical-align top 
      line-height 24px
      margin 12px 0 0 12px
      color rgba(255,255,255,0.4)
      font-size 10px
   .content-right
     flex 0 0 105px
     width 105px
     .pay
        height 48px
        line-height :48px
        text-align :center
        font-size :12px
        color rgba(255,255,255,0.4)
        font-weight:700   
        &.not-enough
         background :#2b333b
        &.enough
         background :#00b43c
         color :#fff

  .ball-container
  //外层做纵向运动
    .ball
      position :fixed
      left:32px
      bottom:22px
      width: 16px
      height 16px
      z-index:200
      //y轴 贝塞尔曲线
      &.drop-enter-active,&.drop-leave-active
       transition: all 0.4s cubic-bezier(0.49,-0.29,0.75,0.41)
      //x轴只需线性缓动
      .inner
        width 16px
        height 16px
        border-radius 50%
        background:rgb(0,160,220)
        transition: all 0.4s linear
  .shopcart-list
    position absolute
    left 0
    top 0
    z-index -1
    width 100%
    transform :translate3d(0,-100%,0)
    &.fold-enter,&.fold-leave-to
      transform :translate3d(0,0,0)
    &.fold-enter-active, &.fold-leave-active
      transition all linear .5s
    .list-header
      height 40px
      line-height 40px
      padding 0 18px
      background:#f3f5f7
      border-bottom 1px solid rgba(7,17,27,0.1)
      .title
        float left
        font-size 14px
        color rgb(7,17,27)
      .empty
        float right
        font-size 12px
        color rgb(0,160,220)
    .list-content
        padding 0 18px
        max-height 217px
        background #fff
        text-align left
        overflow hidden
        .food
          position relative
          padding 12px 0
          box-sizing border-box
          border-bottom 1px solid rgba(7,17,27,0.1)
        .name
          line-height 24px
          font-size 14px
          color rgb(7,17,27)
        .price
          position absolute
          right 90px
          bottom 12px
          font-size 14px
          line-height 24px
          color rgb(240,20,20)
          font-weight 700
        .cartcontrol-wrapper
          position absolute
          right 0
          bottom 6px
			
</style>
