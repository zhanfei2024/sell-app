<template>
  <div class="seller" v-el:seller>
    <div class="seller-wrapper">
      <div class="seller-important">
        <div class="seller-star">
          <h1>{{seller.name}}</h1>
          <div><star :size="36" :score="seller.score"></star><span class="sell-title">(661)</span><span  class="sell-count">月售{{seller.sellCount}}单</span></div>
          <div class="favorite" @click="toggle($event)">
            <span class="icon-favorite" :class="{'collection': favorite}"></span>
            <span class="favorite-text">{{favoriteText}}</span>
          </div>
        </div>
        <div class="seller-support">
          <ul>
            <li><p>起送价</p><p>{{seller.minPrice}}<span>元</span></p></li><li><p>商家配送</p><p>{{seller.deliveryPrice}}<span>元</span></p></li><li><p>平均配送时间</p><p>{{seller.deliveryTime}}<span>分钟</span></p></li>
          </ul>
        </div>
      </div>
      <div class="seller-footer"></div>
      <div class="seller-activity">
        <h1>公告与活动</h1>
        <p>{{seller.bulletin}}</p>
        <div class="support-detail">
          <div v-if="seller.supports" v-for="item in seller.supports" class="support-list">
            <span class="icon" :class="classMap[item.type]"></span>
            <span class="text">{{item.description}}</span>  
          </div>
        </div>
      </div>
      <div class="seller-footer"></div>
      <div class="seller-photo">
        <h1>商家实景</h1>
        <div class="photo-wrapper" v-el:photo>
          <div class="photo-inner">
            <ul>
              <li v-for="item in seller.pics" class="photo-item"><img :src="item" class="pic"></li>
            </ul>
          </div>
        </div>
      </div>
      <div class="seller-footer"></div>
      <div class="seller-info">
        <h1>商家信息</h1>
        <ul>
          <li v-for="item in seller.infos">{{item}}</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
import BScroll from 'better-scroll';
import {saveToLocal, loadFromLocal} from 'common/js/store';
import Star from 'components/star/Star.vue';
export default {
  data() {
    return {
      seller: {},
      favorite: (() => {
        return loadFromLocal(this.seller.id, 'favorite', false);
      })()
    };
  },
  props: {
    seller: {
      type: Object
    }
  },
  computed: {
    favoriteText() {
      return this.favorite ? '收藏' : '未收藏';
    }
  },
  methods: {
    toggle(event) {
      if (!event._constructed) {
        return;
      }
      this.favorite = !this.favorite;
      // 需要将收藏的商家信息保存到本地缓存
      saveToLocal(this.seller.id, 'favorite', this.favorite);
    }
  },
  created() {
    this.$nextTick(() => {
      this.scroll = new BScroll(this.$els.seller, {
          click: true
      });
      this.photoScroll = new BScroll(this.$els.photo, {
          click: true,
          scrollY: false,
          scrollX: true
      });
    });
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
  },
  components: {
    Star
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl";
.seller
  position: absolute
  top: 174px
  bottom: 0
  left: 0
  width: 100%
  overflow: hidden
  .seller-footer
    width: 100%
    height: 16px
    background: rgb(243,246,246)
    border-1px(rgba(7, 17, 27, 0.1))
  .seller-important
    border-1px(rgba(7, 17, 27, 0.1))
    padding: 0px 18px
    .seller-star
      padding 18px 0
      border-1px(rgba(7, 17, 27, 0.1))
      h1
        margin-bottom 8px
        font-size: 14px
        font-weight 700
        line-height: 14px
        color: rgb(7, 17, 27)
      div
        .star.star-36
          display: inline-block
          vertical-align middle
        .sell-title
          margin-right: 12px
          font-size: 10px
          line-height: 18px
          color: rgb(77, 85, 93)
        .sell-count
          font-size: 10px
          line-height: 18px
          color: rgb(77, 85, 93)
      .favorite
        position absolute
        width 36px
        bottom 18px
        right -4px
        text-align center
        .icon-favorite
          display block
          padding-bottom 4px
          font-size 24px
          line-height 24px
          color rgb(147, 153, 159)
          &.collection
              padding-bottom 4px
              font-size 24px
              line-height 24px
              color rgb(240, 20, 20)
        .favorite-text
          display block
          font-size 12px
          -webkit-transform: scale(0.8)
          line-height 10px
          color rgb(77, 85, 93)
    .seller-support
      padding 18px 0
      ul
        display: flex
        li
          flex: 1
          text-align: center
          border-right: 1px solid rgba(7, 17, 27, 0.1)
          &:last-child
            border-right: none
          p
            &:first-child
              font-size: 10px
              line-height: 10px
              color: rgb(147, 153, 159)
              padding-bottom: 4px
          p
            &:last-child
              font-size: 24px
              line-height: 24px
              font-weight: 200
              color: rgb(7, 17, 27)
              span
                font-size: 10px
                line-height: 24px
                font-weight: 200
                color: rgb(7, 17, 27)
  .seller-activity
    padding: 18px 18px 0
    border-1px(rgba(7, 17, 27, 0.1))
    h1
      font-size: 14px
      font-weight 700
      line-height: 14px
      color: rgb(7, 17, 27)
    p
      padding: 8px 12px 16px 12px
      font-size: 12px
      line-height: 24px
      color: rgb(240, 20, 20)
      font-weight: 200
      border-1px(rgba(7, 17, 27, 0.1))
    .support-detail
      .support-list
        padding: 16px 12px
        border-1px(rgba(7, 17, 27, 0.1))
        &:last-child
          border none
        .icon
          display:inline-block
          vertical-align: middle
          width: 16px
          height: 16px
          background-size:16px 16px
          background-repeat:no-repeat
          &.decrease
            bg-image('decrease_4')
          &.discount
            bg-image('discount_4')
          &.special
            bg-image('special_4')
          &.invoice
            bg-image('invoice_4')
          &.guarantee
            bg-image('guarantee_4')
        .text
          margin-left: 6px
          display:inline-block
          font-size:12px
          line-height:16px
          font-weight: 200
          color: rgb(7, 17, 27)
  .seller-photo
    padding: 18px
    border-1px(rgba(7, 17, 27, 0.1))
    h1
      padding-bottom: 12px
      font-size: 14px
      font-weight 700
      line-height: 14px
      color: rgb(7, 17, 27)
    .photo-wrapper
      height 90px
      position: relative
      .photo-inner
        position: absolute
        margin-bottom -120px
        width: 700px
        height: 90px
        ul 
          width: 700px
          font-size: 0
          li
            padding-right: 6px
            display: inline-block
          .pic
            width: 120px
            height: 90px
  .seller-info
    padding: 18px 18px 0
    h1
      padding-bottom: 12px
      font-size: 14px
      font-weight 700
      line-height: 14px
      color: rgb(7, 17, 27)
      border-1px(rgba(7, 17, 27, 0.1))
    ul
      li
        padding: 16px 12px
        font-size: 12px
        line-height: 16px
        font-weight: 200
        color: rgb(7, 17, 27)
        border-1px(rgba(7, 17, 27, 0.1))

</style>

