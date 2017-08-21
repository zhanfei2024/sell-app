<template>
    <div class="detail" v-show="showFlag" v-el:detail-wrapper>
        <div class="detail-wrapper">
                <div class="pic">
                <img :src="food.image" class="img">
                </div>
                <div class="name">
                    <h1 class="food-name">{{food.name}}</h1>
                    <p class="food-sell"><span class="food-sellCount">月售{{food.sellCount}}份</span><span class="food-rating">好评率{{food.rating}}%</span></p>
                    <p class="price"><span>￥</span><span>{{food.price}}</span><span>￥</span><span>{{food.oldPrice}}</span></p>
                    <cart-control :food="food" class="cartcontrol" v-show="food.count>0"></cart-control>
                    <div class="shoppCart" v-show="!food.count || food.count===0"  @click="addToCart">加入购物车</div>
                </div>
                <split></split>
                <div class="info" v-show="food.info">
                    <h1 class="title">商品介绍</h1>
                    <p class="text">{{food.info}}</p>
                </div>
                <split v-show="food.info"></split>
                <div class="food-rating">
                    <h1>商品评价</h1>
                    <select-ratings @select="selectRating" @toggle="toggleContent" :select-type="selectType" :only-read="onlyRead" :desc="desc" :ratings="food.ratings"></select-ratings>
                    <div class="rating-content">
                        <ul>
                            <li v-show="needShow(rating.rateType,rating.text)" v-for="rating in food.ratings" class="rating-list">
                                <div class="rating-left">
                                    <div class="rating-time">{{convertTime(new Date(rating.rateTime))}}</div>
                                    <div class="rating-type">
                                        <span :class="{'icon-thumb_up': rating.rateType===0}" v-if="rating.rateType===0"></span>
                                        <span :class="{'icon-thumb_down': rating.rateType===1}" v-if="rating.rateType===1"></span>
                                        <span class="rating-text">{{rating.text}}</span>
                                    </div>
                                </div>
                                <div class="rating-right">
                                    <span class="rating-name">{{rating.username}}</span><img :src="rating.avatar" class="rating-avatar">
                                </div>
                            </li>
                        </ul>
                    </div>
                </div>
        </div>
    </div>
</template>

<script  type="text/ecmascript-6">
import Vue from 'vue';
import BScroll from 'better-scroll';
import CartControl from 'components/cartcontrol/cartcontrol';
import SelectRatings from 'components/selectRatings/selectRatings';
import Split from 'components/split/split';
// const ALL = 2;
export default {
    data() {
        return {
            showFlag: false,
            selectType: 2,
            onlyRead: false,
            desc: {
                all: '全部',
                positive: '推荐',
                negative: '吐槽'
            }
        };
    },
    props: {
        food: {
            type: Object
        }
    },
    methods: {
        show: function() {
            this.showFlag = true;
            this.selectType = 0;
            this.onlyRead = false;
            this.$nextTick(() => {
            if (!this.scroll) {
                this.scroll = new BScroll(this.$els.detailWrapper, {
                click: true
                });
            } else {
                this.scroll.refresh();
            }
            });
        },
        hide: function() {
            this.showFlag = false;
        },
        convertTime: function(date) {
            let y = date.getFullYear();
            let m = date.getMonth() + 1;
            m = m < 10 ? ('0' + m) : m;
            let d = date.getDate();
            d = d < 10 ? ('0' + d) : d;
            let h = date.getHours();
            let minute = date.getMinutes();
            minute = minute < 10 ? ('0' + minute) : minute;
            return y + '-' + m + '-' + d + ' ' + h + ':' + minute;
        },
        addToCart: function() {
            Vue.set(this.food, 'count', 1);
        },
        needShow(rateType, text) {
            if (!text && this.onlyRead) {
                return false;
            } else if (this.selectType === 2) {
                return true;
            } else {
                return rateType === this.selectType;
            }
        },
        selectRating(type) {
            this.selectType = type;
            this.$nextTick(() => {
            this.scroll.refresh();
            });
        },
        toggleContent() {
            this.onlyRead = !this.onlyRead;
            this.$nextTick(() => {
            this.scroll.refresh();
            });
        }
    },
    components: {
        CartControl,
        Split,
        SelectRatings
    }
};
</script>

<style lang="stylus" rel="stylesheet/stylus">
    @import '../../common/stylus/mixin.styl';
    .detail
        position: absolute
        z-index:0
        left: 0
        top: 0
        bottom: 48px
        overflow: hidden
        background: rgb(255, 255,255)
        .detail-wrapper
            flex: 0 0 100%
            width: 100%
            .pic
                .img
                    display: inline-block
                    width: 100%
                    height: 375px
            .name
                position: relative
                padding: 18px
                background: rgb(255, 255,255)
                .food-name
                    font-size: 14px
                    line-height: 14px
                    font-weight: 700
                    color: rgb(7, 17, 27)
                .food-sell
                    padding-top: 8px
                    font-size: 10px
                    line-height: 10px
                    color: rgb(147, 153, 159)
                    .food-sellCount
                        padding-right: 12px
                    .food-rating
                        padding 0
                .price
                    padding-top: 18px
                    span:nth-child(1)
                        font-size: 10px
                        font-weight: normal
                        line-height: 24px
                        color: rgb(240, 20, 20)
                    span:nth-child(2)
                        font-size: 14px
                        font-weight: 700
                        line-height: 24px
                        color: rgb(240, 20, 20)
                    span:nth-child(3)
                        font-size: 10px
                        font-weight: normal
                        line-height: 24px
                        color: rgb(147, 153, 159)
                    span:nth-child(4)
                        font-size: 14px
                        font-weight: 700
                        line-height: 24px
                        color: rgb(147, 153, 159)
                .cartcontrol
                    position absolute
                    right 12px
                    bottom 12px
                .shoppCart
                    position: absolute
                    right: 18px
                    bottom: 18px
                    width: 74px
                    height: 24px
                    background-color: rgb(0, 160, 220)
                    border-radius: 12px
                    text-align: center
                    line-height: 24px
                    font-size: 10px
                    color: rgb(255, 255, 255)
            
            .info
                padding: 18px
                background: rgb(255, 255,255)
                .title
                    font-size: 14px
                    line-height: 14px
                    font-weight: 700
                    color: rgb(7, 17, 27)
                .text
                    padding: 6px 8px 0 8px
                    font-size: 12px
                    font-weight: 200
                    line-height: 24px
                    color: rgb(77, 85, 93)
            .food-rating
                background: rgb(255, 255,255)
                padding: 18px 18px 46px 18px
                h1
                    padding-bottom 6px
                    font-size: 14px
                    line-height: 14px
                    font-weight: 700
                    color: rgb(7, 17, 27)
                .rating-content
                    .rating-list
                        width: 100%
                        padding: 16px 0
                        display: flex
                        border-1px(rgba(7, 17, 27, 0.1))
                        .rating-left
                            flex: 0 0 220px
                            width: 220px
                            .rating-time
                                font-size: 10px
                                line-height: 12px
                                color: rgb(147, 153, 159) 
                            .rating-type
                                padding-top: 6px
                                .icon-thumb_up
                                    font-size 12px
                                    color rgb(0, 160, 220)
                                    line-height 32px
                                .icon-thumb_down
                                    font-size 12px
                                    color rgb(183, 187, 191)
                                    line-height 32px
                                .rating-text
                                    font-size: 12px
                                    line-height: 16px
                                    color: rgb(7, 17, 2)
                        .rating-right
                            flex: 1
                            .rating-name
                                padding-right: 6px
                                font-size: 10px
                                line-height: 12px
                                color: rgb(147, 153, 159)
                            .rating-avatar
                                display: inline-block
                                vertical-align: middle
                                width: 12px
                                height: 12px
</style>
