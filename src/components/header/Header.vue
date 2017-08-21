<template>
  <div class="header">
    <div class="content-wrapper">
      <div class="avatar">
        <img width="64" height="64" :src="seller.avatar">
      </div>
      <div class="content">
        <div class="title">
          <div class="brand"></div>
          <div class="name">{{seller.name}}</div>
        </div>
        <div class="description">
          {{seller.description}}/{{seller.deliveryTime}}分钟送达
        </div>
        <div v-if="seller.supports" class="support">
          <span class="icon" :class="classMap[seller.supports[0].type]"></span><span class="text">
          {{seller.supports[0].description}}</span>  
        </div>
      </div>
      <div v-if="seller.supports" class="support-count" @click="showDetail">
        <span class="count">{{seller.supports.length}}个</span>
        <span class="icon-keyboard_arrow_right"></span>
      </div>
    </div>
    <div class="bulletin-wrapper"  @click="showDetail">
      <span class="bulletin-title"></span><span class="bulletin-text">{{seller.bulletin}}</span>
      <span class="icon-keyboard_arrow_right"></span>
    </div>
    <div class="background">
      <img :src="seller.avatar" width="100%" height="100%">
    </div>
    <div class="detail-info" v-show="detailShow" transition="fade">
      <div class="detail-wrapper clearfix" v-el:ratings-wrapper>
        <div class="detail-main">
          <h1 class="name">{{seller.name}}</h1>
          <star :size="48" :score="seller.score"></star>
          <div class="seller-discount">
            <div class="subtitle">
              <div class="subtitle-box">
                  <div class="subtitle-content">优惠信息</div>
              </div>
            </div>
            <div class="support">
              <div v-if="seller.supports" v-for="item in seller.supports" class="support-list">
                <span class="icon" :class="classMap[item.type]"></span>
                <span class="text">{{item.description}}</span>  
              </div>
            </div>
          </div>
          <div class="seller-notice">
            <div class="subtitle">
              <div class="subtitle-box">
                <div class="subtitle-content">商家公告</div>
              </div>
            </div>
            <div class="info">
              {{seller.bulletin}}
            </div>
          </div>
        </div>
      </div>
      <div class="detail-close" @click="hideDetail">
        <span class="icon-close"></span>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
import Star from 'components/star/Star.vue';
import BScroll from 'better-scroll';
export default {
  props: {
    seller: {
      type: Object
    }
  },
  data () {
    return {
      detailShow: false
    };
  },
  methods: {
    _initScroll: function() {
      this.meunScroll = new BScroll(this.$els.ratingsWrapper, {});
    },
    showDetail() {
      this.detailShow = true;
    },
    hideDetail() {
      this.detailShow = false;
    }
  },
  components: {
    Star
  },
  created() {
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl";
  .header
    position: relative
    color: #ffffff
    background: rgba(7, 12, 27, 0.5)
    .content-wrapper
      position: relative
      padding:24px 12px 18px 24px
      font-size:0
      .avatar
        display inline-block
        vertical-align top
        img
          border-radius 2px
      .content
        display inline-block
        vertical-align top
        padding-left:16px
        font-size:14px
        .title
          margin: 2px 0 8px 0
          font-size 0
          .brand
            display:inline-block
            vertical-align: top
            width: 30px
            height: 18px
            border-radius:2px
            bg-image('brand')
            background-size:30px 18px
            background-repeat:no-repeat
          .name
            margin-left: 6px
            display:inline-block
            font-size:16px
            font-weight:bold
            line-height:18px
        .description
            font-size:12px
            font-weight:200
            line-height:12px
            margin-bottom:10px
        .support
            .decrease
              display:inline-block
              vertical-align: middle
              width: 12px
              height: 12px
              background-size:12px 12px
              background-repeat:no-repeat
              &.decrease
                bg-image('decrease_1')
              &.discount
                bg-image('discount_1')
              &.special
                bg-image('special_1')
              &.invoice
                bg-image('invoice_1')
              &.guarantee
                bg-image('guarantee_1')
            .text
              margin-left: 4px
              display:inline-block
              font-size:10px
              line-height:12px
      .support-count
        position: absolute
        right: 12px
        bottom: 18px
        padding: 0 8px
        height: 24px
        line-height: 24px
        border-radius: 14px
        background: rgba(0, 0, 0, 0.2)
        text-align: center
        .count
          vertical-align: top
          font-size: 10px
        .icon-keyboard_arrow_right
          margin-left: 2px
          line-height: 24px
          font-size: 10px
    .bulletin-wrapper
      position: relative
      height:28px
      line-height: 28px
      padding: 0 22px 0 12px
      white-space: nowrap
      overflow: hidden
      text-overflow ellipsis
      background-color: rgba(7, 17, 27, 0.2)
      .bulletin-title
        display:inline-block
        vertical-align: middle
        width: 22px
        height: 12px
        bg-image('bulletin')
        background-size: 22px 12px
        background-repeat: no-repeat
      .bulletin-text
        margin: 0 4px
        font-size: 10px
      .icon-keyboard_arrow_right 
        position: absolute
        font-size: 10px
        right: 12px
        top: 8px
    .background
      position: absolute
      z-index: -999
      left: 0
      top: 0
      width: 100%
      height: 100%
      filter: blur(10px)
    .detail-info
      position: fixed
      z-index:999
      left: 0
      top: 0
      width: 100%
      height: 100%
      overflow: auto
      transition all 0.3s
      backdrop-filter blur(10px)
      &.fade-transition
        opacity 1
        background rgba(7, 12, 27, 1)
      &.fade-enter, &.fade-leave
        opacity 0
        background rgba(7, 17, 27, 0)
      .detail-wrapper
        min-height 100%
        .detail-main
          padding-top 64px
          padding-bottom 64px
          .name
            margin-bottom 16px
            width: 100%
            text-align: center
            font-size: 16px
            font-weight: 700
            line-height: 16px
            color: #ffffff
          .star-48
            margin-bottom 28px
          .seller-discount
            margin: 0 36px
            .subtitle
              display: flex
              &:before
                content: ""
                display: inline-block
                position relative
                top -9px
                margin-right: 12px
                height: 1px
                width: 30%
                flex-grow: 1
                background-color: rgba(255, 255, 255, 0.2)
                margin-top: 18px
              &:after
                content: ""
                display: inline-block
                position relative
                top -9px
                margin-left: 12px
                height: 1px
                width: 30%
                flex-grow: 1
                background-color: rgba(255, 255, 255, 0.2)
                margin-top: 18px
              .subtitle-content
                flex-grow: 1
            .support
              margin: 16px auto 22px
              .support-list
                margin: 6px 0 6px 12px
                .icon
                  display:inline-block
                  vertical-align: middle
                  width: 16px
                  height: 16px
                  background-size:16px 16px
                  background-repeat:no-repeat
                  &.decrease
                    bg-image('decrease_1')
                  &.discount
                    bg-image('discount_1')
                  &.special
                    bg-image('special_1')
                  &.invoice
                    bg-image('invoice_1')
                  &.guarantee
                    bg-image('guarantee_1')
                .text
                  margin-left: 6px
                  display:inline-block
                  font-size:12px
                  line-height:12px
          .seller-notice
            margin: 0 36px
            .subtitle-box
              display: flex
              &:before
                content: ""
                display: inline-block
                position relative
                top -9px
                margin-right: 12px
                height: 1px
                width: 30%
                flex-grow: 1
                background-color: rgba(255, 255, 255, 0.2)
                margin-top: 18px
              &:after
                content: ""
                display: inline-block
                position relative
                top -9px
                margin-left: 12px
                height: 1px
                width: 30%
                flex-grow: 1
                background-color: rgba(255, 255, 255, 0.2)
                margin-top: 18px
              .subtitle-content
                flex-grow: 1
            .info
              font-size: 12px
              line-height: 24px
              font-weight: 200
              margin: 24px 12px auto
      .detail-close
          position relative
          margin -64px auto 0 auto
          text-align center
          font-size 32px
          color rgba(255, 255, 255, 0.5)

 </style>
