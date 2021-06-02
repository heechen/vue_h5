<template>
  <div class="goods">
    <div class="menu-wrapper" ref="menuref">
      <ul>
        <li :data-s=index ref="menuitemref" v-for="(item,index) in goods" class="menu-item menu-list-hook" :class="{'current':currentIndex === index}" :key=index @click="selectMenu(index,$event)">
        <!-- <li ref="menuitemref" v-for="(item,index) in goods" class="menu-item menu-list-hook" :class="[currentIndex === index ?current:'',]" :key=index @click="selectMenu(index,$event)"> -->
          <span class="text border-1px">
            <span v-show="item.type>0" class="icon" :class="classMap[item.type]"></span>{{item.name}}
          </span>
        </li>
      </ul>
    </div>
    <div class="foods-wrapper" ref="foodsref">
      <ul>
        <li v-for="item in goods" class="food-list food-list-hook">
          <h1 class="title">{{item.name}}</h1>
          <ul>
            <li v-for="food in item.foods"  class="food-item border-1px">
              <div class="icon">
                <img :src="food.icon"  width="57" height="57">
              </div>
              <div class="content">
                <h2 class="name">{{food.name}}</h2>
                <p class="desc">{{food.description}}</p>
                <div class="extra">
                  <span class="count">月售{{food.sellCount}}份</span>
                  <span>好评{{food.rating}}%</span>
                </div>
                <div class="price">
                  <span class="new">￥{{food.price}}</span>
                  <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import BScroll from "better-scroll";
export default {
//  props:{
//    seller:{
//      type:Object
//    }
//  },
 data(){
    return{
      goods:[],
      listHeight:[],
      scrollY:0,
      isActive: 0
    
    }
  },
computed:{
    currentIndex(){
      let menuList = this.$refs.menuref.querySelectorAll('.menu-list-hook')
      for (let i = 0; i < this.listHeight.length; i++) {
        let height1 = this.listHeight[i]
        let height2 = this.listHeight[i + 1]
        //最后一个元素height2为undefined(最后一个元素) 或 落在对应区间
        if(!height2 || (this.scrollY >= height1 && this.scrollY < height2)){
          let menuel = menuList[i];
          this.meunScroll.scrollToElement(menuel,300,0,true,'easing')
          console.log(i);
          return i
        }
      }
       return 0
    }
  },
 created(){
   this.classMap = ['decrease','discount','special','invoice','guarantee']
   this.$http.get('/api/goods').then((response)=>{
       this.goods = (response.data.data);
       this.$nextTick(()=>{
        this._inintScroll()
        this._calculateHeight()

       })
    
    }).catch(function(error){
        console.log(error);
    })

   
 },
 methods:{
   _inintScroll() {
     this.meunScroll = new BScroll(this.$refs.menuref,{
       click:true
     })
      this.foodsScroll =new BScroll(this.$refs.foodsref,{
       probeType:3,
      //  mouseWheel: true
     })
      this.foodsScroll.on('scroll',(pos)=>{
       this.scrollY = Math.abs(Math.round(pos.y))
     })
    
   },
   _calculateHeight(){
    
     let foodList= this.$refs.foodsref.querySelectorAll('.food-list-hook')
     
    
     //console.log(this.$refs.foodsref.getElementsByClassName('food-list-hook'));
     let height = 0 
     this.listHeight.push(height)
     for (let i = 0; i < foodList.length; i++) {
       let item = foodList[i]
       height += item.clientHeight
       this.listHeight.push(height) 
     }
   },
   selectMenu(index,event){
    if(!event._constructed){
      return
    }
    let foodList = this.$refs.foodsref.querySelectorAll('.food-list-hook')
    let el = foodList[index]
    this.foodsScroll.scrollToElement(el,300)

    let menuList = this.$refs.menuref.querySelectorAll('.menu-list-hook')      
    let menuel = menuList[index];
    this.meunScroll.scrollToElement(menuel,300,0,true,'easing')
     
   }
 }
}
</script>

<style lang="stylus">
@import '../../commom/stylus/minx.styl';
.goods
  display :flex
  position : absolute
  top:174px
  bottom :200px
  width :100%
  overflow :hidden
  .menu-wrapper
    flex : 0 0 80px
    width :80px
    background :#f3f5f7
    .menu-item
      display : table
      height :54px
      width:56px
      padding :0 12px
      // line-height:14px
      // font-size:14px
      &.current
        position :relative
        z-index :10
        margin-top -1px
        background #fff
        font-weight 700
        .text
          border-none()
      .icon
        display :inline-block
        vertical-align :top
        width :12px
        height :12px
        margin-right :2px
        background-size:12px 12px
        background-repeat:no-repeat
        &.decrease
          bg-image('decrease_3')
        &.discount
          bg-image('discount_3')
        &.guarantee
          bg-image('guarantee_3')
        &.invoice
          bg-image('invoice_3')
        &.special
          bg-image('special_3')
      .text
        display : table-cell
        width :56px
        vertical-align :middle
        font-size :12px
        border-1px(rgba(7,17,27,0.2))
  .foods-wrapper
    flex :1
    .title
      padding-left :14px
      height :26px
      line-height:26px
      border-left:2px solid #d9dde1
      font-size 12px
      color:rgb(147,153,159)
      background:#f3f5f7
    .food-item
      display flex
      margin:18px
      padding-bottom :18px
      border-1px(rgba(7,17,27,0.2))
      &:last-child
        border-none()
        margin-bottom :0
      .icon
        flex :0 0 57px
        margin-right :10px
      .content
        flex :1
        .name
          margin 2px 0 8px 0
          height 14px
          line-height 14px
          font-size 14px
          color rgb(7,17,27)
        .desc,.extra
          line-height 10px
          font-size 10px
          color:rgb(147,153,159)
        .desc
          margin-bottom :8px
          line-height 12px
        .extra
          .count
            margin-right :12px
        .price
          font-weight 700
          line-height 24px
          .new
            margin-right 8px
            font-size 14px
            color rgb(240,20,20)
          .old
            text-decoration:line-through
            font-size:10px
            color:rgb(147,153,159)
</style>
