<template>
    <div class="cartcontrol">
        <div class="cartcontrol-wrapper">
            <div class="cartcontrol-decrease" v-show="food.count>0" transition="move" @click.stop.prevent="_decreaseFood">
                <span class="inner icon-remove_circle_outline"></span>
            </div>
            <div class="cartcontrol-num" v-show="food.count>0">{{food.count}}</div>
            <div class="cartcontrol-add" @click.stop.prevent="_addFood">
                <span class="icon-add_circle"></span>
            </div>
        </div>
    </div>
</template>

<script  type="text/ecmascript-6">
import Vue from 'vue';
export default {
    props: {
        food: {
            type: Object,
            count: 0
        }
    },
    methods: {
        _addFood: function() {
            if (!this.food.count) {
                Vue.set(this.food, 'count', 1);
            } else {
                this.food.count++;
            }
        },
        _decreaseFood: function() {
            if (!this.food.count) {
                Vue.set(this.food, 'count', 0);
            } else {
                this.food.count--;
            }
        }
    }
};
</script>

<style lang="stylus" rel="stylesheet/stylus">
    @import '../../common/stylus/mixin.styl';
    .cartcontrol
        .cartcontrol-wrapper
            display table
            font-size 0
            .cartcontrol-decrease, .cartcontrol-num, .cartcontrol-add
                display table-cell
                padding 0px
            .cartcontrol-decrease
                transition: all 0.4s linear
                &.move-transition
                    opacity 1
                    transform: translate3D(0, 0, 0)
                .inner
                    display: inline-block
                    font-size 24px
                    line-height 24px
                    vertical-align middle
                    color rgb(0, 160, 220)
                    transition: all 0.4s linear
                    transform: rotate(0)
                &.move-enter, &.move-leave
                    opacity: 0
                    transform: translate3D(24px, 0, 0)
                    .inner
                        transform: rotate(180deg)
            .cartcontrol-num
                width 12px
                text-align center
                vertical-align top
                font-size 10px
                line-height 24px
                color rgb(147, 153, 159)
            .cartcontrol-add
                font-size 24px
                line-height 24px
                vertical-align middle
                color rgb(0, 160, 220)
</style>
