<template>
  <div class="cartcontrol">
    <transition name="move">
        <div class="inner cart-detcontrol icon-remove_circle_outline" @click.stop.preven="detCart" v-show="food.count > 0"></div>
    </transition>
      <div class="cart-count" v-show="food.count > 0">{{food.count}}</div>
    <div class="cart-add icon-add_circle" @click.stop.preven="addCart"></div>
  </div>
</template>

<script>
 import Vue from 'vue';
 export default {
   name: 'cartcontrol',
   props: {
     food: {
       type: Object
     }
   },
   created() {
//     console.log(this.food);
   },
   methods: {
     addCart(event) {
       console.log(!this.food.count);
       if (!this.food.count) {
         Vue.set(this.food, 'count', 1);  // 修改父级元素的时候  使用Vue设置
//         this.food.count = 1;
       } else {
         this.food.count++;
       }
     },
     detCart() {
       this.food.count--;
     }
   }
 };
</script>
<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin";
  .cartcontrol
    display flex
    .cart-detcontrol
      display inline-block
      padding:3px
      line-height: 24px
      font-size: 24px
      color: rgb(0, 160, 220)
      transition: all 0.4s linear
      transform: rotate(0)
      &.move-enter-active, &.move-leave-active
        transition: all 0.4s linear
      &.move-enter, &.move-leave-active
        opacity: 0
        transform: translate3d(24px, 0, 0)
    .cart-count
      display inline-block
      height 25px
      line-height 26px
      width:10px;
      text-align center
    .cart-add
      display inline-block
      padding 3px
      font-size 24px
      color #00a0dc
</style>
