<template>
  <div>
    <div class="goods">
        <div class="goods-left"  ref="menuList">
          <ul>
              <li class="item" ref="menuLists" @click="selectMenu(index,$event)"  v-for="(item,index) in goods" :class="{'current':currentIndex===index}"><span class="span">
                <span v-show="item.type>0" class="icon" :class="classMap[item.type]"></span>{{item.name}}</span></li>
          </ul>
        </div>
        <div class="goods-right" ref="foodList">
          <ul>
            <li v-for="(item,index) in goods" ref="foodHeightList">
              <h1 class="title">{{item.name}}</h1>
              <ul>
                <li  @click="selectFood(items,$event)" v-for="(items,indexs) in item.foods" >
                  <div  class="items">
                      <div class="icon">
                        <img width="57" height="57" :src="items.icon"></div>
                      <div class="content">
                        <div class="name">{{items.name}}</div>
                        <div class="desc">{{items.description}}</div>
                        <div class="extra">月售{{items.sellCount}}份 好评率{{items.rating}}%</div>
                        <div class="price">
                          <span class="now">￥{{items.price}}</span>
                          <span class="old" v-show="items.oldPrice">￥{{items.oldPrice}}</span>
                        </div>
                      </div>
                    <div class="cartcontrols">
                      <cartcontrol :food="items"></cartcontrol>
                    </div>
                  </div>
                </li>
              </ul>
            </li>
          </ul>
        </div>
      <shopcart :selectFoods="selectFoods" :seller="seller"  ref="shopcart"></shopcart>
      <food :food="selectFod" ref="food"></food>
    </div>
  </div>
</template>
<script>
  import BScroll from 'better-scroll';
  import shopcart from '@/components/shopcart/shopcart';
  import cartcontrol from '@/components/cartcontrol/cartcontrol';
  import food from '@/components/food/food';
  import json from '@/common/json/data';
//  const ERR_OK = 0;

  export default {
    name: 'goods',
    props: {
      seller: {
        type: Object
      }
    },
    data() {
      return {
        goods: [],
        listHeight: [],
        scrollY: 0,
        selectFod: {}
      };
    },
    created() {
      console.log(json);
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
//      this.$http.get('/api/goods').then((response) => {});     // 因为需要放置到GitHub 视图中显示数据是通过import导入
//        response = response.body;
//        if (response.errno === ERR_OK) {};
      this.goods = json.goods;
//      console.log(response.data);
      this.$nextTick(() => {
        this._initScroll();
        this._calculateHeight();
      });
    },
    computed: {
      currentIndex() {  // 监听滚动范围  是否在
        for (let i = 0; i < this.listHeight.length; i++) {
          let height1 = this.listHeight[i];
          let height2 = this.listHeight[i + 1];
          if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
            this._followScroll(i);
            return i;
          }
        }
        return 0;
      },
      selectFoods() {
        let foods = [];
        this.goods.forEach((good) => {
          good.foods.forEach((food) => {
            if (food.count) {
              foods.push(food);
            }
          });
        });
        return foods;
      }
    },
    components: {
      shopcart,
      cartcontrol,
      food
    },
    methods: {
      selectFood(food, e) {  // 在这里调用子组件的方法
        this.selectFod = food;
        this.$refs.food.show();
      },
      selectMenu(index, event) {
        if (!event._constructed) {  // 阻止双击事件
          return;
        }
        let foodList = this.$refs.foodHeightList;
        let el = foodList[index];
        this.foodScroll.scrollToElement(el, 300);
      },
      _followScroll(index) {
        let menuList = this.$refs.menuLists;
        let el = menuList[index];
        this.meunScroll.scrollToElement(el, 300, 0, -100);
      },
      _initScroll() {
        this.meunScroll = new BScroll(this.$refs.menuList, {
          click: true
        });
        this.foodScroll = new BScroll(this.$refs.foodList, {
          click: true,
          probeType: 3
        });
        this.foodScroll.on('scroll', (pos) => {
          // 判断滑动方向，避免下拉时分类高亮错误（如第一分类商品数量为1时，下拉使得第二分类高亮）
          if (pos.y <= 0) {
            this.scrollY = Math.abs(Math.round(pos.y));
//            console.log(this.scrollY);
          }
        });
      },
      _calculateHeight() { // 将分类得高度存储到   全局声明的数组中
        let foodList = this.$refs.foodHeightList;
        let height = 0;
        this.listHeight.push(height);
        for (let i = 0; i < foodList.length; i++) {
          let item = foodList[i];
          height += item.clientHeight;
          this.listHeight.push(height);
          console.log(height);
        };
      }
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin";
  .goods
    display flex
    position absolute
    bottom 46px
    top 174px
    overflow hidden
    width 100%
    .current
      background #fff
    .goods-left
      flex  0 0 80px
      width:80px;
      background: #f3f5f7
      .item
        height 54px
        z-index 1
        width: 54px;
        padding 0 12px
        display table
        .span
          display table-cell
          font-size 12px
          vertical-align middle
          border-1px(rgba(7, 17, 27, 0.2))
        .icon
          display: inline-block;
          vertical-align: top;
          width: 12px;
          height: 12px;
          margin-right: 2px;
          background-size: 12px 12px;
          background-repeat: no-repeat;
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
    .goods-right
      flex 1
      .title
        padding-left 14px
        height 26px
        line-height: 26px;
        border-left: 2px solid #d9dde1;
        font-size: 12px;
        color: #93999f;
        background: #f3f5f7;
      .items
        display flex
        margin: 18px 18px 0 18px
        padding-bottom 18px
        font-size 12px
        position relative
        .content
          position relative
          padding 1px 0
          .name
            margin 4px 0 0
          .desc
            color: #93999f
            font-size 10px
            margin 4px 0 0
          .text
            color #93999f
            margin 4px 0 0
          .extra
            color #93999f
            margin 4px 0 0
        .cartcontrols
          position absolute
          right:0;
          bottom 12px
        .now
          color: red;
          font-size 15px
          font-width:700;
        .old
          font-size 10px
          text-decoration line-through
          color #93999f
        img
          margin-right 10px

</style>
