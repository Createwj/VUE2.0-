<template>
  <div ref="ratings" class="ratings-s">
    <div class="content" >
      <div class="head">
        <div class="left">
          <div class="score">{{seller.score}}</div>
          <div class="score-h">综合评分</div>
          <div class="score-g">高于周边商家{{seller.rankRate}}%</div>
        </div>
        <div class="right">
          <div class="shop">
            <span class="title">服务态度</span>
            <star :size="36" :score="seller.serviceScore"></star>
            <i>{{seller.serviceScore}}</i>
          </div>
          <div class="shop">
            <span class="title">商品评价</span>
            <star :size="36" :score="seller.foodScore"></star>
            <i>{{seller.foodScore}}</i></div>
          <div class="shop">
            <span class="title">送达时间</span>
            <b>{{seller.deliveryTime}}分钟</b>
          </div>
        </div>
      </div>
      <split></split>
      <div class="rating-content">
        <div class="ratingselects">
          <ratingselect @select="select" @toggle="onlyCont" :rating="ratings" :desc="desc" :onlyContent="onlyContent" :selectType="selectType"></ratingselect>
        </div>
        <div class="listWraper">
          <ul>
            <li v-show="needShow(item.rateType,item.text)" v-for="(item,index) in ratings">
              <div class="avatar">
                <img :src="item.avatar">
              </div>
              <div class="cont">
                <div class="user">
                  <div class="title">{{item.username}}</div>
                  <div class='conts'>{{item.rateTime | formatDate}}</div>
                </div>
                <div>
                  <star :size="36" :score="item.score"></star>
                  <span class="scores">{{item.deliveryTime}}</span>
                </div>
                <div class="text">{{item.text}}</div>
                <div class="tags" v-show="item.recommend && item.recommend.length">
                  <span class="icon-thumb_up"></span>
                  <span class="tag" v-for="item in item.recommend">{{item}}</span>
                </div>
              </div>
            </li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
  import split from '@/components/split/split';
  import star from '@/components/star/star';
  import {formatDate} from '@/common/js/date';
  import ratingselect from '@/components/ratingselect/ratingselect';
  import BScroll from 'better-scroll';
  import json from '@/common/json/data';
  const ALL = 2;
//  const ERR_OK = 0;
  export default {
    name: 'ratings',
    props: {
      seller: {
        type: Object
      }
    },
    data() {        // 内部生声明数据
      return {
        selectType: ALL,  // 全选
        onlyContent: true, // 初始化的时候展示
        desc: {      // 传递给组件显示的对象
          all: '全部',
          positive: '满意',
          negative: '不满意'
        },
        ratings: []
      };
    },
    created() {
      this.ratings = json.ratings;
      this.$nextTick(() => {
        this.scroll = new BScroll(this.$refs.ratings, {
          click: true
        });
      });
//      this.$http.get('/api/ratings').then((response) => {
//        response = response.body;
//        if (response.errno === ERR_OK) {
//          this.ratings = response.data;
//          this.$nextTick(() => {
//            this.scroll = new BScroll(this.$refs.ratings, {
//              click: true
//            });
//          });
//        }
//      });
    },
    methods: {
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
    filters: {
      formatDate(time) {
        let date = new Date(time);
        return formatDate(date, 'yyyy-MM-dd hh:mm');
      }
    },
    components: {
      split,
      star,
      ratingselect
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin";
  .ratings-s
    position absolute
    top 174px
    bottom 0
    z-index -2
    overflow hidden
  .head
    display flex
    padding 18px 0
    .left
      width 137px
      text-align center
      .score
        font-size 24px
        color #f90
        margin-bottom 6px
      .score-h
        color #07111b
        font-size 12px
        margin-bottom 9px
      .score-g
        font-size 10px
        color #999
    .right
      padding 6px 0 6px 24px
      .title
        font-size 10px
      .star
        display: inline-block;
        margin: 0 12px;
        vertical-align: top;
      .shop
        margin-bottom 9px
        i
          font-size 10px
          color #f90
        b
          font-size 11px
          color #999
          padding-left 5px
  .rating-content
    .ratingselects
      margin 0 15px
    .listWraper
      li
        display flex
        padding 18px 18px
        .avatar
          flex 0 0 28px
          width 28px
          margin-right 12px
          img
            width 100%
            border-radius 100%
        .cont
          width 100%
          .user
            display flex
            justify-content space-between
            font-size 14px
            margin-bottom 7px
            color #888
          .star
            margin-bottom 4px
            display inline-block
          .scores
            color #888
            display inline-block
            font-size 10px
            margin-left 7px
            line-height 18px
            vertical-align top
          .text
            line-height 18px
            font-size 12px
            margin-bottom 5px
          .tags
            color #00a0dd
            .tag
              border 1px solid #ddd
              font-size 10px
              color #999
              padding 2px 5px
              display inline-block
              margin-top 3px

</style>
