<template>
  <div ref="seller" class="seller">
    <div class="content">
        <div class="heade">
          <div class="left">
            <div class="name">{{seller.name}}</div>
            <star :size="36" :score="seller.score"></star>
            <span class="shou">（{{seller.ratingCount}}） 月售{{seller.sellCount}}单</span>
          </div>
          <div class="right" @click="toggle">
            <p class="icon-favorite " :class="{'active':shou === true}"></p>
            <p class="text">{{text}}</p>
          </div>
        </div>
        <div class="headerBody">
          <div class="block">
            <p class="title">起送价</p>
            <p>
              <span class="text">{{seller.minPrice}}</span><span class="dan">元</span>
            </p>
          </div>
          <div class="block">
            <p class="title">商家派送</p>
            <p>
              <span class="text">{{seller.deliveryPrice}}</span><span class="dan">元</span>
            </p>
          </div>
          <div class="block">
            <p class="title">平均派送时间</p>
            <p>
              <span class="text">{{seller.deliveryTime}}</span><span class="dan">分钟</span>
            </p>
          </div>
        </div>
        <split></split>
        <div class="body">
          <div class="info">
            <h1>公告与活动</h1>
            <p>{{seller.bulletin}}</p>
          </div>
          <ul class="infomat" v-if="seller.supports">
            <li v-for="(item, index) in seller.supports">
              <span class="icon" :class="classMap[item.type]"></span>
              <span class="desc">{{item.description}}</span>
            </li>
          </ul>
        </div>
        <split></split>
        <div class="shopont" >
          <h1>商家实景</h1>
          <div class="pic-wrapper" ref="picWrapper">
            <ul>
              <li v-for="item in seller.pics">
                <img :src="item" width="120" height="90">
              </li>
            </ul>
          </div>

        </div>
      <split></split>
      <div class="shopinfo">
          <h1>商家信息</h1>
        <ul>
          <li v-for="item in seller.infos">{{item}}</li>
        </ul>
      </div>
    </div>
  </div>
</template>
<script>
  import BScroll from 'better-scroll';
  import split from '@/components/split/split';
  import star from '@/components/star/star';
  export default {
    name: 'seller',
    props: {
      seller: {
        type: Object
      }
    },
    data() {
      return {
        shou: false,
        text: '收藏'
      };
    },
    created() {
      console.log(this.seller);
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
      this.$nextTick(() => {
        setTimeout(() => {
          this.scroll = new BScroll(this.$refs.seller, {
            click: true
          });
          this.scrolls = new BScroll(this.$refs.picWrapper, {
            scrollX: true,
            eventPassthrough: 'vertical'
          });
        }, 300);
      });
    },
    methods: {
      toggle() {
        this.shou = !this.shou;
        if (this.shou) {
          this.text = '已收藏';
        } else {
          this.text = '收藏';
        };
      }
    },
    components: {
      split,
      star
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin";
  .seller
    position absolute
    top: 173px
    bottom 0
    width 100%
    overflow hidden
    .content
      .heade
        padding 18px 18px
        display flex
        justify-content space-between
        padding-bottom 18px
        .right
          width 50px
        .left
          font-size 0
          .name
            font-size 14px
            margin-bottom 7px
          .shou
            font-size 12px
            display inline-block
            color #999
            vertical-align top
            line-height 18px
          .star
            display inline-block
        .right
          text-align center
          .icon-favorite
            font-size 24px
            padding 4px 3px
            color #d4d6d9
            &.active
              color #f01414
          .text
            font-size 10px
            color #999
      .headerBody
        padding 0 18px
        font-size 12px
        display flex
        margin-bottom 18px
        .block
          flex 1
          margin-top 15px
          text-align center
          .title
            text-align center
            color #999
            padding-bottom 4px
          .text
            font-size 24px

      .body
        padding 18px 18px 0 18px
        .info
          p
            padding 10px 14px
            color #f01414
            font-size 12px
            line-height 20px
        .infomat
          padding 8px 15px
          li
            margin 14px 0
          .icon
            display inline-block
            vertical-align top
            width 18px
            height 18px
            margin-right 4px
            background-size 18px 18px
            background-repeat  no-repeat
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
          .desc
            color #07111b
            line-height 16px
            font-size 12px

      .shopont
        padding 18px
        overflow hidden
        h1
          margin-bottom 15px
        .pic-wrapper
          overflow hidden
          ul
            height 90px
            width 498px
            display flex
            li
              margin 3px

      .shopinfo
        padding 18px
        h1
          margin-bottom 15px
        ul
          padding-left 15px
          font-size 13px
          li
            margin 29px 0
</style>
