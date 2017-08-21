<template>
<div class="cart">
  <div class="shop-cart" @click="toggleList">
    <div class="cart-left">
        <div class="logo-wrapper">
            <div class="logo" :class="{'highlight': totalCount>0}">
                <span class="icon-shopping_cart" :class="{'highlight': totalCount>0}"></span>
            </div>
            <div class="num" v-show="totalCount>0">{{totalCount}}</div>
        </div>
        <div class="price" :class="{'highlight': totalPrice>0}">￥{{totalPrice}}</div>
        <div class="desc">另需配送费￥{{deliveryPrice}}元</div>
    </div>
    <div class="cart-right" :class="{'highlight': totalPrice>=minPrice}" @click="pay">{{payDesc}}</div>
  </div>
  <div class="cart-list" v-show="listShow">
    <div class="header">
        <h1 class="name">购物车</h1>
        <p class="clear" @click="clearShopCart">清空</p>
    </div>
    <div class="content" v-el:list-content>
        <ul class="content-wrapper">
            <li class="food" v-for="food in selectFoods">
                <div class="food-name">{{food.name}}</div>
                <div class="price">
                    <span class="food-totalpricelable">￥</span><span class="food-totalprice">{{food.count*food.price}}</span>
                </div>
                <div class="cart-control">
                    <cart-control :food="food"></cart-control>
                </div>
            </li>
        </ul>
    </div>
  </div>
</div>
<div class="mask" v-show="listShow" transition="fade" @click="hidden"></div>
</template>

<script  type="text/ecmascript-6">
import BScroll from 'better-scroll';
import CartControl from 'components/cartcontrol/cartcontrol';
export default {
    props: {
        selectFoods: {
            type: Array
        },
        flod: true,
        deliveryPrice: {
            type: Number,
            default: 4
        },
        minPrice: {
            type: Number,
            default: 0
        }
    },
    data() {
      return {
        fold: true
      };
    },
    computed: {
        totalPrice() {
            let total = 0;
            this.selectFoods.forEach((food) => {
                total += food.price * food.count;
            });
            return total;
        },
        totalCount() {
            let count = 0;
            this.selectFoods.forEach((food) => {
                count += food.count;
            });
            return count;
        },
        payDesc() {
            if (this.totalPrice === 0) {
                return `￥${this.minPrice}元配送`;
            } else if (this.totalPrice < this.minPrice) {
                let diff = this.minPrice - this.totalPrice;
                return `还差￥${diff}元配送`;
            } else {
                return '去结算';
            }
        },
        listShow() {
            if (!this.totalCount) {
                this.flod = true;
                return false;
            }
            let show = !this.flod;
            if (show) {
                this.$nextTick(() => {
                    if (!this.scroll) {
                        this.scroll = new BScroll(this.$els.listContent, {
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
            this.flod = !this.flod;
        },
        clearShopCart() {
            this.selectFoods.forEach((food) => {
                food.count = 0;
            });
        },
        hidden() {
            this.flod = true;
        },
        pay() {
            if (this.totalPrice < this.minPrice) {
                return;
            }
            window.alert(`你支付￥${this.totalPrice}元`);
        }
    },
    components: {
        CartControl
    }
};
</script>

<style lang="stylus" rel="stylesheet/stylus">
    @import "../../common/stylus/mixin";
    .cart
        .shop-cart
            position: fixed
            z-index 100
            left: 0
            bottom: 0
            display: flex
            width: 100%
            height: 45px
            background-color: #141d27
            .cart-left
                flex: 1
                .logo-wrapper
                    float left
                    display: inline-block
                    position relative
                    top -10px
                    width 56px
                    height 56px
                    margin 0 18px
                    border-radius 56px
                    background-color #141d27
                    .logo
                        position: absolute
                        left 6px
                        top 6px
                        z-index 300
                        display inline-block
                        width 44px
                        height 44px
                        border-radius 44px
                        background-color rgba(220, 220, 220, 0.2)
                        &.highlight
                            background: rgb(0, 160, 220)
                        text-align center
                        line-height 32px
                        .icon-shopping_cart
                            font-size: 24px
                            line-height 44px
                            color rgb(255, 255, 255, 0.4)
                            &.highlight
                                color: rgb(255, 255, 255)
                    .num
                        position absolute
                        z-index 400
                        right 0
                        top 0
                        width 24px
                        line-height 16px
                        border-radius 8px
                        font-size 12px
                        font-weight 700
                        text-align center
                        color rgb(255, 255, 255)
                        background-color rgb(240, 20, 20)
                        box-shadow 0px 4px 8px 0px rgba(0, 0, 0, 0.4)
                .price
                    float left
                    margin: 12px 12px 12px 0
                    padding-right 12px
                    font-size: 16px
                    line-height: 24px
                    font-weight: 700
                    color: rgba(255, 255, 255, 0.4) 
                    &.highlight
                        color: rgb(255, 255, 255) 
                    border-right: 1px solid rgba(255, 255, 255, 0.1)
                .desc
                    float left
                    margin: 12px
                    font-size: 16px
                    line-height: 24px
                    font-weight: 400
                    color: rgba(255, 255, 255, 0.4)
            .cart-right
                flex: 0 0 106px
                width: 106px
                padding: 12px 8px
                font-size: 14px
                line-height: 24px
                font-weight: 700
                text-align center
                color: rgba(255, 255, 255, 0.4)
                background-color RGB(43,51,59)
                &.highlight
                    background-color RGB(28,179,70)
                    color: rgb(255, 255, 255)
        .cart-list
            position absolute
            z-index 99
            left 0
            bottom 44px
            width 100%
            background-color rgb(255, 255, 255)
            .header
                width 100%
                height 40px
                line-height 40px
                background-color #f3f5f7
                .name
                    padding-left 18px
                    line-height 40px
                    font-size 14px
                    font-weight 200
                    color rgb(7, 17, 27)
                    float left
                .clear
                    padding-right 18px
                    font-size 12px
                    line-height 40px
                    color rgb(0, 160, 220)
                    float right
            .content
                padding 0 18px
                max-height 258px
                overflow hidden
                .content-wrapper
                    width 100%
                    .food
                        display table
                        position relative
                        padding 12px 0
                        border-1px(rgba(7, 17, 27, 0.1)) 
                        .food-name, .price, .cart-control
                            display table-cell
                            vertical-align middle
                            width 40%
                        .food-name
                            font-size 14px
                            color rgb(7, 17, 27)
                            line-height 24px
                        .price
                            // position absolute
                            // right 90px
                            // bottom 12px
                            padding-right 12px
                            line-height 24px
                            .food-totalpricelable
                                font-size 10px
                                font-weight normal
                                color rgb(240, 20, 20)
                                line-height 24px
                            .food-totalprice
                                font-size 14px
                                font-weight normal
                                color rgb(240, 20, 20)
                                line-height 24px
                        .cart-control
                            // position absolute
                            // right 0
                            // bottom 5px
                            font-size 24px
                            line-height 24px
                            color rgb(7, 17, 27)
    .mask
        position fixed
        top 0
        left 0
        width 100%
        height 100%
        z-index 98
        background-color rgba(7, 17, 27,0.8)
        filter blur(10px)
        &.fade-transition
            transition all 0.4s
            opacity 1
            background: rgba(7, 17, 27, 0.9)
        &.fade-enter, &.fade-leave
            opacity 0
            background rgba(7, 17, 27, 0)
</style>
