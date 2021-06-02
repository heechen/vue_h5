<template>
  <div>
    <button type="button" @click="show = !show">往返动画按钮</button>
    <transition name="my-transition">
      <p v-show="show">
        <span>我是要进行往返动画的文字</span>
      </p>
    </transition>
  </div>
</template>

<script>
export default {
  data() {
    return {
      show: false
    };
  }
};
</script>

<style lang="stylus">
.my-transition-leave-to,
.my-transition-enter
  opacity: 0


.my-transition-enter-active,
.my-transition-leave-active 
  transition: opacity 1s

</style>