<template>
  <div id="app">
    <v-header :seller=seller></v-header>
    <div class="tab border-1px">
      <div class="tab-item">
        <router-link to="/goods">商品</router-link>
        </div>
      <div class="tab-item">
         <router-link to="/ratings">评论</router-link></div>
      <div class="tab-item">
         <router-link to="/seller">商家</router-link></div>
    </div>
    <router-view :seller = "seller"></router-view>
  </div>
</template>

<script>
import Header from "@/components/header/Header"
export default {
  data(){
    return{
      seller:{}
    }
  },
  created(){
    this.$http.get('/api/seller').then((response)=>{
       this.seller = (response.data.data);
      
    }).catch(function(error){
        console.log(error);
    })
  },
  
  components:{
    "v-header":Header
  }
}
</script>

<style lang="stylus">
   @import "./commom/stylus/minx.styl"
  .tab
    display:flex
    width: 100%
    height :40px
    line-height :40px
    //border-bottom :1px solid  rgba(7,17,27,0.5)
    border-1px(rgba(7,17,27,0.5))
    .tab-item
      flex:1
      text-align :center
      & > a
        display :block
        font-size: 14px
        color: rgb(77,85,93)
        &.router-link-active
          color :rgb(240,20,20)

       
</style>
