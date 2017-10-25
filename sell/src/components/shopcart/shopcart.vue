<template>
  <div>
    <div class="shopcart">
      <div class="content" >
        <div class="left" @click="toggleList">
          <div class="icons">
            <div class="logo" :class="{'light':totalCount > 0}">
              <i class="icon-shopping_cart" :class="{'light':totalCount > 0}"></i>
            </div>
            <div class="num" v-show="totalCount > 0">{{totalCount}}</div>
          </div>
          <div class="price" :class="{'light':totalCount > 0}">￥{{totalPrice}}元</div>
          <div class="desc">另需派送费{{seller.deliveryPrice}}元</div>
        </div>
        <div class="right">
          <div class="pay" @click="pay" :class="payClass">{{payDesc}}</div>
        </div>
      </div>
      <transition name="move">
        <div class="shopcart-list" v-show = "listShow">
          <div class="list-head">
            <div class="title">购物车</div>
            <div class="empty" @click="empty"> 清空</div>
          </div>
          <div class="list-content" ref="shopList">
            <ul>
              <li class="food" v-for="food in selectFoods">
                <span>{{food.name}}</span>
                <div class="price">
                  <span>￥{{food.price*food.count}}</span>
                </div>
                <div class="cartcontrol-wrapper">
                  <cartcontrol :food="food"></cartcontrol>
                </div>
              </li>
            </ul>
          </div>
        </div>
      </transition>
      <transition name="move">
      <div class="list-mask" v-show="listShow" @click="hiddenModal"></div>
      </transition>
    </div>

  </div>
</template>
<script>
  import BScroll from 'better-scroll';
  import cartcontrol from '@/components/cartcontrol/cartcontrol';
  export default {
    name: 'shopcart',
    props: {
      selectFoods: {
        type: Array,
        default() {
          return [];
        }
      },
      seller: {
        type: Object
      }
    },
    created() {
      console.log(this.selectFoods);
    },
    data() {
      return {
        fold: true
      };
    },
    computed: {
      payClass() { // 去结算按钮颜色控制
        if (this.totalPrice < this.seller.minPrice) {
          return `not-enough`;
        } else {
          return 'enough';
        }
      },
      payDesc() {  // 去结算按钮字体控制
        if (this.totalPrice === 0) {
          return `${this.seller.minPrice}元起送`;
        } else if (this.totalPrice < this.seller.minPrice) {
          let prc = this.seller.minPrice - this.totalPrice;
          return `还差${prc}元`;
        } else {
          return '去结算';
        }
      },
      totalPrice() {  // 总价计算
        let total = 0;
        this.selectFoods.forEach((food) => {
          total += food.price * food.count;
        });
        return total;
      },
      totalCount() {  // 添加商品的计算
        let count = 0;
        this.selectFoods.forEach((food) => {
          count += food.count;
        });
        return count;
      },
      listShow() {
        if (!this.totalCount) {
          this.fold = true;
          return false;
        };
        let show = !this.fold;
//        if (show) {
//          this.$nextTick(() => {
//            if (!this.meunScroll) {
//              this.meunScroll = new BScroll(this.$refs.shopList, {
//                click: true
//              });
//            } else {
//              this.meunScroll.refresh();
//            }
//          });
//        }
        if (show) {
          this.$nextTick(() => {
            if (!this.scroll) {
              this.scroll = new BScroll(this.$refs.shopList, {
                click: true
              });
            } else {
              this.scroll.refresh();
            }
          });
        }
        return show;
      }
    },
    methods: {
      toggleList() {
        if (!this.totalCount) {
          return;
        }
        this.fold = !this.fold;
      },
      empty() {
        this.selectFoods.forEach((food) => {
          food.count = 0;
        });
      },
      hiddenModal() {
        this.fold = true;
      },
      pay() { // 弹框抽出来 单独写弹框组件
        if (this.totalPrice >= this.seller.minPrice) {
          alert('一共需要支付' + (Number(this.totalPrice) + 4) + '元');
        }
      }
    },
    components: {
      cartcontrol
    }
  };
</script>
<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin";
  .shopcart
    position fixed
    left:0;
    bottom:0;
    height 48px
    width:100%;

    z-index 50
    .content
      display flex
      background #141d27
      .left
        flex:1;
        .icons
          display inline-block
          width:48px;
          height 48px
          margin 0 12px
          padding 6px
          position relative
          top -10px
          vertical-align top
          border-radius 50%
          background #141d27
          .logo
            width: 100%;
            height: 100%;
            border-radius: 50%;
            text-align: center;
            background: #2b343c;
            &.light
              background rgb(0,160,228)
            .icon-shopping_cart
              color #80858a
              font-size 24px
              line-height 48px
              &.light
                color #fff
          .num
            position absolute
            top:0;
            right:0;
            width:24px;
            height:16px;
            line-height 16px
            background rgb(241,21,22)
            text-align center
            border-radius 16px
            font-size 9px
            color #fff
            box-shadow 0 4px 8px 0 rgba(0,0,0,.4)
        .price
          display inline-block
          vertical-align top
          margin:14px 12px 0 0 ;
          color rgba(255,255,255,0.4)
          font-width:700;
          font-size 16px
          &.light
            color #fff
            font-weight 700
        .desc
          display inline-block
          color rgba(255,255,255,0.4)
          vertical-align: top;
          margin: 12px 0 0 12px;
          line-height: 24px;
          font-size: 12px;
      .right
        width 105px
        .pay
          height 48px
          line-height 48px
          font-size 15px
          font-weight 700
          color rgba(255,255,255,0.4)
          text-align center
          background #2b333b
          &.not-enough
            background #2b333b
          &.enough
            background: #00b43c;
            color: #fff;
    .shopcart-list
      position absolute
      bottom 100%
      left:0;
      z-index -1
      width:100%;
      transform translate3d(0,0,0)
      &.move-enter-active, &.move-leave-active
        transition all 0.2s linear
      &.move-enter, &.move-leave-active
        transform: translate3d(0, 100%, 0)
      .list-head
        box-sizing border-box
        width 100%
        background #fff
        display flex
        justify-content:space-between;
        padding:0 18px;
        height 40px
        line-height 40px
        border-bottom 1px solid rgba(7,17,27,0.1)
        .empty
          color #00a0dc
          padding 0 0 0 5px
      .list-content
        padding 0px 18px
        background #fff
        overflow hidden
        max-height 217px
        .food
          padding 12px 0
          line-height 30px
          position relative
          span
            font-size 13px
          .price
            display inline-block
            font-size 10px
            position absolute
            right 90px
            border-bottom 3px
            color red
          .cartcontrol-wrapper
            display inline-block
            position absolute
            right 0
  .list-mask
    position: fixed;
    top: 0;
    left: 0;
    right 0
    bottom 0
    z-index: -3;
    opacity: 1;
    background: rgba(7,17,27,0.6);
    opacity 1
    &.move-enter-active, &.move-leave-active
      transition all 0.2s linear
    &.move-enter, &.move-leave-active
      opacity 0
</style>
