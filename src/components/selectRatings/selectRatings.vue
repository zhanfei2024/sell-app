<template>
    <div class="select-ratings">
        <div class="switch">
            <span class="all" :class="{'active': selectType===2}" @click="select(2, $event)">{{desc.all}}<span class="num">{{ratings.length}}</span></span>
            <span class="positive" :class="{'active': selectType===0}" @click="select(0, $event)">{{desc.positive}}<span class="num">{{positives.length}}</span></span>
            <span class="negative" :class="{'active': selectType===1}" @click="select(1, $event)">{{desc.negative}}<span class="num">{{negatives.length}}</span></span>
        </div>
        <div class="only-read">
            <span class="icon-check_circle" :class="{'active': onlyRead}" @click="toggleContent"></span><span class="text">只看有内容的评价</span>
        </div>
    </div>
</template>

<script  type="text/ecmascript-6">
const POSITIVE = 0;
const NEGATIVE = 1;
const ALL = 2;
export default {
    props: {
        ratings: {
            type: Array,
            default() {
                return [];
            }
        },
        selectType: {
            type: Number,
            default: ALL
        },
        onlyRead: {
            type: Boolean,
            default: false
        },
        desc: {
            type: Object,
            default() {
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
            return this.ratings.filter((rating) => {
                return rating.rateType === POSITIVE;
            });
        },
        negatives() {
            return this.ratings.filter((rating) => {
                return rating.rateType === NEGATIVE;
            });
        }
    },
    methods: {
        select(type, event) {
            if (!event._constructed) {
            return;
            }
            this.$emit('select', type);
        },
        toggleContent(event) {
            if (!event._constructed) {
            return;
            }
            this.$emit('toggle');
        }
    }
};
</script>

<style lang="stylus" rel="stylesheet/stylus">
    @import '../../common/stylus/mixin.styl';
    .select-ratings
        border-1px(rgba(7, 17, 27, 0.1))
        .switch
            padding 18px 0
            margin 0 18px
            border-1px(rgba(7, 17, 27, 0.1))
            font-size 0
            .all
                margin-right 8px
                padding 8px 12px
                text-align center
                line-height 16px
                font-size 12px
                color rgb(77, 85, 93)
                border-radius 2px
                background-color rgba(0, 160, 220, 0.2)
                &.active
                    background-color rgb(0, 160, 220)
                    color rgb(255, 255, 255)
                .num
                    display inline-block
                    font-size 12px
                    -webkit-transform scale(0.8)
            .positive
                margin-right 8px
                padding 8px 12px
                text-align center
                line-height 16px
                font-size 12px
                color rgb(77, 85, 93)
                border-radius 2px
                background-color rgba(0, 160, 220, 0.2)
                &.active
                    background-color rgb(0, 160, 220)
                    color rgb(255, 255, 255)
                .num
                    display inline-block
                    font-size 12px
                    -webkit-transform scale(0.8)
            .negative
                padding 8px 12px
                text-align center
                line-height 16px
                font-size 12px
                color rgb(77, 85, 93)
                border-radius 2px
                background-color rgba(77, 85, 93, 0.2)
                &.active
                    background-color rgb(77, 85, 93)
                    color rgb(255, 255, 255)
                .num
                    display inline-block
                    font-size 12px
                    -webkit-transform scale(0.8)
    .only-read
        padding 12px 0 12px 18px
        .icon-check_circle
            margin-right 4px
            font-size 24px
            line-height 24px
            color rgb(147, 153, 159)
            vertical-align middle
            &.active
                color rgb(0, 160, 220)
        .text
            font-size 12px
            line-height 24px
            color rgb(147, 153, 159)
</style>
