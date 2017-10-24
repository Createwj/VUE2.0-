<template>
  <div class="ratingselect">
    <div class="rating-type">
      <span class="block positive" @click = "select(2,$event)" :class="{'active':selectType === 2}">{{desc.all}} <span class="count">{{rating.length}}</span></span>
      <span class="block positive" @click = "select(0,$event)" :class="{'active':selectType === 0}">{{desc.positive}} <span class="count">{{positives.length}}</span></span>
      <span class="block negative" @click = "select(1,$event)" :class="{'active':selectType === 1}">{{desc.negative}} <span class="count">{{negatives.length}}</span></span>
    </div>
    <div class="switch">
      <span @click="toggleContent" class="icon-check_circle" :class="{'on':onlyContent}"></span>
      <span class="text">只看有内容的评价</span>
    </div>
  </div>
</template>
<script  type="text/ecmascript-6">
const POSITIVE = 0; // 积极评价
const NEGATIVE = 1;  // 消极版本
const ALL = 2;
export default {
  name: 'ratingselect',
  props: {
    rating: {  // 数据
      type: Array,
      default() {
        return [];
      }
    },
    selectType: {  // 通过这个变量 去控制 切换 默认全部展示
      type: Number,
      default: ALL
    },
    onlyContent: { // 读内容
      type: Boolean,
      default: false
    },
    desc: {   // 描述
      type: Object,
      default() { // 默认返回数据
        return {
          all: '全部',
          positive: '满意',
          negative: '不满意'
        };
      }
    }
  },
  computed: {
    positives() {
      return this.rating.filter((rating) => {
        return rating.rateType === POSITIVE;
      });
    },
    negatives() {
      return this.rating.filter((rating) => {
        return rating.rateType === NEGATIVE;
      });
    }
  },
  methods: {
    select(num, e) {
      this.$emit('select', num); // 给父组件派发事件
      console.log(e);
    },
    toggleContent() {
      this.$emit('toggle', !this.onlyContent);
    }
  }
};
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin";
    .ratingselect
      .rating-type
        padding 18px 0
        border-1px(rgba(7,17,27,.1))
        font-size 0
        .block
          display inline-block
          padding 8px 12px
          font-size 12px
          border-radius 2px
          margin-right 8px
          color rgba(77,85,93,.5)
          &.active
            color #fff
          &.positive
            background rgba(0,160,221,.2)
            &.active
              background rgb(0,160,221)
          &.negative
            background rgba(77,85,93,.5)
            &.active
              background rgb(77,85,93,)
      .switch
        padding 12px 0
        line-height 24px
        font-size 0
        .icon-check_circle
          font-size 24px
          vertical-align middle
          &.on
            color rgb(0,160,221)
        .text
          margin-left 3px
          font-size 13px
          color #999
          vertical-align top
</style>
