<template>
  <div class="ratings">
    <div class="ratings-wrapper"  v-el:ratings-wrapper>
      <div class="ratings-inner">
        <div class="ratings-important">
          <div class="ratings-left">
            <div class="left-inner">
              <p class="score">{{seller.score}}</p>
              <p class="title">综合评分</p>
              <p class="rank">高于周边商家{{seller.rankRate}}%</p>
            </div>
          </div>
          <div class="ratings-right">
            <ul>
              <li class="seller-service"><span class="ratings-title">服务态度</span><star :size="36" :score="seller.score"></star><span class="score">{{seller.score}}</span></li>
              <li class="seller-attitude"><span class="ratings-title">服务态度</span><star :size="36" :score="seller.serviceScore"></star><span class="score">{{seller.serviceScore}}</span></li>
              <li class="seller-time"><span class="ratings-title">送到时间</span><span class="time">{{seller.deliveryTime}}分钟</span></li>
            </ul>
          </div>
        </div>
        <split></split>
        <div class="ratings-content">
          <select-ratings @select="selectRating" @toggle="toggleContent" :select-type="selectType" :only-read="onlyRead" :desc="desc" :ratings="ratings"></select-ratings>
          <div class="rating-list">
            <ul>
              <li  v-show="needShow(rating.rateType, rating.text)" class="rating" v-for="rating in ratings">
                <div class="rating-left"><img :src="rating.avatar"></div>
                <div class="rating-right">
                  <h1 class="username">{{rating.username}}</h1>
                  <p class="score"><star :size="36" :score="rating.score"></star><span class="time" v-show="rating.deliveryTime">{{rating.deliveryTime}}分钟送达</span></p>
                  <p class="text">{{rating.text}}</p>
                  <p class="recommend" v-show="rating.recommend && rating.recommend.length">
                    <span :class="{'icon-thumb_up': rating.rateType===1}" v-if="rating.rateType===1"></span>
                    <span :class="{'icon-thumb_down': rating.rateType===0}" v-if="rating.rateType===0"></span>
                    <span class="recommend-list" v-for="item in rating.recommend">{{item}}</span>
                  </p>
                  <div class="rating-time">{{convertTime(new Date(rating.rateTime))}}</div>
                </div>
              </li>
            </ul>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
import Star from 'components/star/Star.vue';
import Split from 'components/split/split';
import SelectRatings from 'components/selectRatings/selectRatings';
import BScroll from 'better-scroll';
const ERR_OK = 0;
export default {
  data: function () {
    return {
      seller: {},
      ratings: {},
      selectType: 2,
      onlyRead: false,
      desc: {
          all: '全部',
          positive: '满意',
          negative: '不满意'
      }
    };
  },
  props: {
    seller: {
      type: Object
    }
  },
  methods: {
    _initScroll: function() {
      this.meunScroll = new BScroll(this.$els.ratingsWrapper, {});
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
  created () {
    this.$http.get('/api/ratings').then((response) => {
      response = response.body;
      if (response.errno === ERR_OK) {
        this.ratings = response.data;
        this.$nextTick(() => {
          this._initScroll();
        });
      }
      this.selectType = 0;
      this.onlyRead = false;
      this.$nextTick(() => {
      if (!this.scroll) {
        this.scroll = new BScroll(this.$els.ratingsWrapper, {
        click: true
        });
      } else {
        this.scroll.refresh();
      }
      });
    });
  },
  components: {
    Star,
    Split,
    SelectRatings
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl";
.ratings
  position: absolute
  z-index -1
  top: 174px
  bottom: 0
  left: 0
  display: flex
  width: 100%
  overflow: hidden
  .ratings-wrapper
    .ratings-important
      display: flex
      background: rgb(255, 255, 255)
      .ratings-left
        padding: 18px 0
        flex: 0 0 137px
        width: 137px
        text-align: center
        @media only screen and (max-width: 320px)
          flex: 0 0 120px
          width: 120px
        .left-inner
          border-right: 1px solid rgba(7, 17, 27, 0.1)
          .score
            padding-bottom: 6px
            font-size: 24px
            line-height: 28px
            color: rgb(255, 153, 0)
          .title
            padding-bottom: 8px
            font-size: 12px
            line-height: 12px
            color: rgb(7, 17, 27)
          .rank
            padding-bottom: 6px
            font-size: 10px
            line-height: 10px
            color: rgb(147, 153, 159)
      .ratings-right
        flex: 1
        padding: 18px 0 18px 24px
        @media only screen and (max-width: 320px)
          padding-left: 6px
        .seller-service
          padding-bottom: 8px
          .ratings-title
            padding-right: 12px
            font-size: 12px
            line-height: 18px
            color: rgb(7, 17, 27)
            @media only screen and (max-width: 320px)
              padding-right 8px
          .star.star-36
            padding-right: 12px
            display: inline-block
            vertical-align: middle
            margin: 0
            @media only screen and (max-width: 320px)
              padding-right 2px
          .score
            font-size: 12px
            line-height: 18px
            color: rgb(255, 153, 0)
        .seller-attitude
          padding-bottom 8px
          .ratings-title
            padding-right: 12px
            font-size: 12px
            line-height: 18px
            color: rgb(7, 17, 27)
            @media only screen and (max-width: 320px)
              padding-right 8px
          .star.star-36
            padding-right: 12px
            display: inline-block
            vertical-align: middle
            margin: 0
            @media only screen and (max-width: 320px)
              padding-right 2px
          .score
            font-size: 12px
            line-height: 18px
            color: rgb(255, 153, 0)
        .seller-time
          .ratings-title
            padding-right: 12px
            font-size: 12px
            line-height: 18px
            color: rgb(7, 17, 27)
            @media only screen and (max-width: 320px)
              padding-right 8px
          .star.star-36
            padding-right: 12px
            display: inline-block
            vertical-align: middle
            margin: 0
            @media only screen and (max-width: 320px)
              padding-right 8px
          .time
            font-size: 12px
            line-height: 18px
            color: rgb(147, 153, 159)
    .ratings-content
      .rating
        position: relative
        display: flex
        margin: 0 18px
        padding 16px 0
        border-1px(rgba(7, 17, 27, 0.1))
        .rating-left
          flex:0 0 28px
          width: 28px
          padding-right 12px
          border-radius: 50%
          img
            display inline-block
            width: 28px
            height: 28px
            border-radius 14px
        .rating-right
          .username
            float left
            width 100%
            padding-bottom: 4px
            font-size: 10px
            // transform scale(0.8)
            line-height: 12px
            color: rgb(7, 17, 27)
          .score
            padding-bottom: 6px
            .star.star-36
              margin: 0
              padding-right: 6px
              display: inline-block
              vertical-align: middle
            .time
              font-size: 10px
              // transform scale(0.8)
              line-height: 12px
              font-weight: 200
              color: rgb(147, 153, 159)
          .text
            padding-bottom: 8px
            font-size: 12px
            line-height: 18px
            color: rgb(7, 17, 27)
          .recommend
            .icon-thumb_up
                font-size 12px
                color rgb(0, 160, 220)
                line-height 32px
            .icon-thumb_down
                font-size 12px
                color rgb(183, 187, 191)
                line-height 32px
            .recommend-list
              margin-right: 8px
              padding: 0 6px
              border: 1px solid rgba(7, 17, 27, 0.1)
              border-radius: 2px
              background-color: rgb(255, 255, 255)
              font-size: 9px
              height 16px
              line-height: 16px
              color: rgb(147, 153, 159)
          .rating-time
            position: absolute
            right: 0
            top: 18px
            font-size: 10px
            font-weight: 200
            line_height: 12px
            color: rgb(147, 153, 159)
 </style>

