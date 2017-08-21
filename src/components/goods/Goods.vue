<template>
  <div class="goods">
    <div class="menu-wrapper" v-el:menu-wrapper>
      <ul>
        <li v-for="item in goods" class="menu-item" :class="{'current' : currentIndex === $index}" @click="selectMenu($index, $event)">
          <span class="text" :class="{'border-1px':currentIndex != $index}" :class="classMap[item.type]">{{item.name}}</span>
        </li>
      </ul>
    </div>
    <div class="foods-wrapper" v-el:foods-wrapper>
      <ul class="foods-content">
        <li v-for="list in goods" class="foods-list foods-list-hook">
          <h1 class="foods-title">{{list.name}}</h1>
          <ul class="foods">
            <li v-for="item in list.foods" class="food" @click="selectedFood(item, $event)">
              <div class="food-img">
                <img :src="item.icon" class="food-src">
              </div>
              <div class="food-info">
                <h2 class="food-title">{{item.name}}</h2>
                <p class="food-desc" v-show="item.description">{{item.description}}</p>
                <div class="food-sell"><span class="sell">月销{{item.sellCount}}份</span><span>好评{{item.rating}}率</span></div>
                <div>
                    <span class="food-price"><span class="first-label">￥</span><span class="second-label">{{item.price}}</span></span>
                    <span v-show="item.oldPrice" class="food-oldPrice"><span class="first-label">￥</span><span class="second-label">{{item.oldPrice}}</span></span>
                </div>
              </div>
              <!--引入购买按钮组件，并且把当前商品对象传递给改组件food-->
              <cart-control :food="item" class="cart-control"></cart-control>
            </li>
          </ul>
        </li>
      </ul>
    </div>
  </div>
  <!--引入商品详情页组件，并且把当前页的点击选中的商品对象传递给改组件food-->
  <food :food="selected" :select-foods="selectFoods" v-ref:food></food>
  <!--引入购物车组件，并且把当前页的点击选中的所有商品对象传递给改组件food-->
  <shop-cart :select-foods="selectFoods" :delivery-price="seller.deliveryPrice" :min-price="seller.minPrice"><shop-cart>
</template>

<script type="text/ecmascript-6">
import BScroll from 'better-scroll';
import Food from 'components/food/food';
import ShopCart from 'components/shopcart/shopcart';
import CartControl from 'components/cartcontrol/cartcontrol';
const ERR_OK = 0;
export default {
  props: {
    seller: {
      type: Object
    }
  },
  data: function() {
    return {
      goods: {},
      listHeight: [],
      scrollY: 0,
      selected: {}
    };
  },
  computed: {
    currentIndex() {
      for (let i = 0; i < this.listHeight.length; i++) {
        let height1 = this.listHeight[i];
        let height2 = this.listHeight[i + 1];
        if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
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
  created: function() {
      this.$http.get('/api/goods').then((response) => {
      response = response.body;
      if (response.errno === ERR_OK) {
        this.goods = response.data;
        this.$nextTick(() => {
          this._initScroll();
          this._calculateHeight();
        });
        }
      });
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
  },
  methods: {
    selectMenu(index, event) {
      if (!event._constructed) {
        return;
      }
      let foodsList = this.$els.foodsWrapper.getElementsByClassName('foods-list-hook');
      let el = foodsList[index];
      this.foodsScroll.scrollToElement(el, 300);
    },
    _initScroll() {
      this.meunScroll = new BScroll(this.$els.menuWrapper, {
        click: true
      });
      this.foodsScroll = new BScroll(this.$els.foodsWrapper, {
        click: true,
        probeType: 3
      });
      this.foodsScroll.on('scroll', (pos) => {
        this.scrollY = Math.abs(Math.round(pos.y));
      });
    },
    _calculateHeight() {
      let foodsList = this.$els.foodsWrapper.getElementsByClassName('foods-list-hook');
      let height = 0;
      this.listHeight.push(height);
      for (let i = 0; i < foodsList.length; i++) {
        height += foodsList[i].clientHeight;
        this.listHeight.push(height);
      }
    },
    selectedFood(food, event) {
      if (!event._constructed) {
        return;
      };
      this.$nextTick(() => {
        this.selected = food;
        this.$refs.food.show();
      });
    }
  },
  components: {
    Food: Food,
    ShopCart: ShopCart,
    CartControl
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl";
  .goods
    position: absolute
    z-index -2
    top: 176px
    bottom: 46px
    display: flex
    width: 100%
    overflow: hidden
    .menu-wrapper
      flex: 0 0 80px
      width: 80px
      background: #f3f5f7
      .menu-item
        display: table
        height: 54px
        width: 56px
        line-height: 14px
        padding: 0 12px
        &.current
          position relative
          margin-top -1px
          z-index 100
          background #fff
          font-weight 700
          .text
            font-size 12px
            font-weight 700
            line-height 14px
            color rgb(240, 20, 20)
            border-1px(rgb(255, 255, 255))
        .icon
          display:inline-block
          vertical-align: middle
          width: 12px
          height: 12px
          margin-right: 2px
          background-size:12px 12px
          background-repeat:no-repeat
          &.decrease
            bg-image('decrease_3')
          &.discount
            bg-image('discount_3')
          &.special
            bg-image('special_3')
          &.invoice
            bg-image('invoice_3')
          &.guarantee
            bg-image('guarantee_3')
        .text
          display: table-cell
          width: 56px
          vertical-align: middle   
          border-1px(rgba(7, 17, 27, 0.1))
          font-size: 12px
    .foods-wrapper
      flex: 1
      .foods-title
        padding-left: 14px
        font-size: 12px
        font-weight 700
        color: rgb(147, 153, 159)
        line-height: 26px
        height: 26px
        border-left: 2px solid #d9dde1
        background: #f3f5f7
      .foods
        color: rgb(147, 153, 159)
        .food
            position relative
            margin: 18px
            font-size 0
            display flex
            .food-img
              display inline-block
              flex 0 0 57px
              width 57px
              .food-src
                display: inline-block
                width: 57px
                height: 57px
            .food-info
              flex 1
              padding 0 10px
              vertical-align: top
              .food-title
                font-size: 14px
                font-weight 700
                color: rgb(7, 17, 27)
                line-height: 14px
                padding-top: 2px
              .food-desc
                padding-top: 8px
                white-space nowrap
                overflow hidden
                text-overflow ellipsis
                font-size 12px
                color: rgb(147, 159, 159)
                line-height: 12px
              .food-sell
                padding-top 8px
                font-size: 10px
                color: rgb(147, 159, 159)
                line-height: 10px
                .sell
                  padding-right 12px
              .food-price
                display block
                float left
                .first-label
                  display inline-block
                  font-size: 12px
                  line-height: 24px
                  font-weight: 200
                  color: rgb(255, 0, 0)
                .second-label
                  display inline-block
                  font-size: 14px
                  line-height: 24px
                  font-weight: 700
                  color: rgb(255, 0, 0)
              .food-oldPrice
                  display block
                  float left
                  transform scale(0.8)
                  text-decoration:line-through
                  .first-label
                    font-size: 10px
                    line-height: 24px
                    font-weight: normal
                    color: rgb(147, 153, 159)
                  .second-label
                    font-size: 10px
                    line-height: 24px
                    font-weight: 700
                    color: rgb(147, 153, 159)
            .cart-control
              position absolute
              right 0px
              bottom 0px



</style>

