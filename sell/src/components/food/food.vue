<template>
  <transition name="move">
  <div v-show="showFlag" class="food" ref="scrList">
    <div class="food-content">
      <div class="img-header">
        <img :src="food.image">
        <div class="back" @click="hide"><i class="icon-arrow_lift"></i></div>
      </div>
      <div class="content">
        <div class="desc">
          <div class="name">{{food.name}}</div>
          <div><span class="shou">月售{{food.sellCount}}份 </span>  <span class="good">好评率{{food.rating}}%</span></div>
          <div class="shop">
            <div class="price">￥{{food.price}}</div>
            <div class="bootCart">
              <div class="add" @click="addCar" v-show="!listShow">加入购物车</div>
              <div class="adds" v-show="listShow" >
                <cartcontrol :food="food" ref="cartcontrol"></cartcontrol>
              </div>

            </div>
          </div>
        </div>
        <split v-show="food.info"></split>
        <div class="info" v-show="food.info">
          <h2 class="title" >商品信息</h2>
          <p class="text">{{food.info}}</p>
        </div>
        <split></split>
        <div class="rating">
            <h1>商品评价</h1>
          <!--传递给子组件信息 select：接收子组件派发事件  rating：用户评论信息  desc：描述信息  selectType：默认选择按钮 onlyContent：只查看内容按钮是否选中-->
          <ratingselect @select="select" @toggle="onlyCont" :rating="food.ratings" :desc='desc' :select-type = 'selectType' :only-content = 'onlyContent'></ratingselect>
        </div>
        <div class="rating-wrapper">
          <ul v-show="food.ratings && food.ratings.length">
            <li v-show="needShow(rating.rateType,rating.text)" v-for="rating in food.ratings" class="rating-item">
              <div class="user">
                <span class="name">{{rating.username}}</span>
                <img :src="rating.avatar" class="avater" width="12" height="12">
              </div>
              <div class="time">{{rating.rateTime | formatDate}}</div>
              <p class="text">
                <span :class="{'icon-thumb_up':rating.rateType===0, 'icon-thumb_down':rating.rateType===1}"></span>{{rating.text}}
              </p>
            </li>
          </ul>
          <div class="no-rating"  v-show="!food.ratings || !food.ratings.length"></div>
        </div>
      </div>
    </div>
  </div>
  </transition>
</template>
<script  type="text/ecmascript-6">
  import BScroll from 'better-scroll'; // 滚动插件
  import {formatDate} from '@/common/js/date';
  import cartcontrol from '@/components/cartcontrol/cartcontrol';  // 购物车添加组件
  import split from '@/components/split/split';  // 空行组件
  import ratingselect from '@/components/ratingselect/ratingselect';

  const ALL = 2;
  export default {
    name: 'food',      // 暴露名称
    data() {        // 内部生声明数据
      return {
        showFlag: false,
        selectType: ALL,  // 全选
        onlyContent: true, // 初始化的时候展示
        desc: {      // 传递给组件显示的对象
          all: '全部',
          positive: '推荐',
          negative: '吐槽'
        }
      };
    },
    props: {      // 接收父组件传递过来的参数
      food: {
        type: Object
      }
    },
    created() {       // 创建的时候执行的函数  相当() => {}()
      console.log(this.food);
    },
    computed: {       // 计算
      listShow() {
        if (this.food.count > 0) {
          return true;
        } else if (this.food.count === 0) {
          return false;
        }
      }
    },
    filters: {
      formatDate(time) {
        let date = new Date(time);
        return formatDate(date, 'yyyy-MM-dd hh:mm');
      }
    },
    methods: {          // 这里面声明一些方法
      needShow(type, text) {  // 2: 全部   1：po  0:nage
        if (this.onlyContent && !text) {
          return false;
        };
        if (this.selectType === ALL) {
          return true;
        } else {
          return type === this.selectType;
        }
      },
      addCar() {
        this.$refs.cartcontrol.addCart();  // 在这里调用子组件的方法
      },
      show() {
        this.selectType = ALL;
        this.onlyContent = false;
        this.$nextTick(() => { // 展示的时候进行滚动渲染
          if (!this.scroll) {
            this.scroll = new BScroll(this.$refs.scrList, {
              click: true
            });
          } else {
            this.scroll.refresh();
          }
        });
        this.showFlag = true;
      },
      hide() {
        this.showFlag = false;
      },
      select(type) {
        this.selectType = type;
        this.$nextTick(() => {
          this.scroll.refresh();
        });
      },
      onlyCont() {
        this.onlyContent = !this.onlyContent;
        this.$nextTick(() => {
          this.scroll.refresh();
        });
      }
    },
    components: {  // 注册组件
      cartcontrol,
      split,
      ratingselect
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin";
  .food
    position fixed
    left 0
    top:0
    bottom 48px
    right 0
    z-index 30
    background #fff
    width 100%
    transform translate3d(0,0,0)
    &.move-enter-active, &.move-leave-active
      transition all 0.2s linear
    &.move-enter, &.move-leave-active
      transform: translate3d(100%, 0, 0)
    .img-header
      position relative
      top:0;
      left 0
      padding-top 100%
      img
        position absolute
        left 0
        top 0
        width 100%
        height 100%
      .back
        position absolute
        padding 10px
        top:10px;
        left 5px
        color #fff
        font-size 29px
    .content
      display block
      .desc
        padding 0 20px
        margin 20px 0
        .name
          font-weight 600
          line-height 14px
          margin-bottom 8px
        .shou, .good
          color #999
          font-size 12px
          line-height 10px
        .shop
          margin-top:19px;
          display flex
          justify-content space-between
          .price
            color: red
            line-height 30px
          .bootCart
            .add
              background: #00a0dc
              height 24px
              line-height 24px
              padding 0 9px
              font-size 10px
              color #ffffff
              border-radius 14px
       .info
          margin 18px
          .title
            font-weight 700
          .text
            padding 15px
            line-height 23px
            font-size 12px
            color #999
      .rating
        margin 0 19px
        h1
          font-weight 700
          margin 19px 0;
    .rating-wrapper
      margin 0 19px
      li
        padding 16px 0
        position relative
        .user
          position absolute
          right 0
          color #999
        .time
          position absolute
          left:0;
          color #999
          font-size 15px
        .text
          margin-top 23px
          .icon-thumb_up
            color #00a0dc
          .icon-thumb_down
            color #999
</style>
