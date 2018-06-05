<template>
  <div class="ratings" ref="ratings">
    <div class="ratings_content">
      <div class="overview">
        <div class="overview_left">
          <h1 class="score">{{ seller.score }}</h1>
          <div class="title">综合评分</div>
          <div class="rank">高于周边商家{{ seller.rankRate }}%</div>
        </div>
        <!----------------------------------------------------------------->
        <div class="overview_right">
          <div class="score-wrapper">
            <span class="title">服务态度</span>
            <star :size="36" :score="seller.serviceScore"></star>
            <span class="score">{{ seller.serviceScore }}</span>
          </div>
          <div class="score-wrapper">
            <span class="title">商品评价</span>
            <star :size="36" :score="seller.foodScore"></star>
            <span class="score">{{ seller.foodScore }}</span>
          </div>
          <!------------------------------------------------------------>
          <div class="delivery-wrapper">
            <span class="title">送达时间</span>
            <span class="delivery">{{ seller.deliveryTime }}分钟</span>
          </div>
        </div>
      </div>
      <split></split>
      <ratingselect :selectType="selectType"
          :onlyContent="onlyContent"
          :ratings="ratings"
          @select="selectRating"
          @toggle="toggleContent"></ratingselect>
      <div class="rating_wrapper">
        <ul>
          <li v-for="(rating,index) in ratings" :key="index" v-show="needShow(rating.rateType,rating.text)" class="rating_item">
            <div class="avatar">
              <img :src="rating.avatar" width="28" height="28" />
            </div>
            <div class="content">
              <h1 class="name">{{ rating.username }}</h1>
              <div class="star-wrapper">
                <star :size="24" :score="rating.score" class="star"></star>
                <span class="delivery" v-show="rating.deliveryTime">{{ rating.deliveryTime }}分钟</span>
              </div>
              <p class="text">{{ rating.text }}</p>
              <div class="recommend">
                <span class="icon-thumb_up" v-show="rating.rateType === 0"></span>
                <span class="icon-thumb_down" v-show="rating.rateType === 1"></span>
                <span class="item" v-for="(item,index) in rating.recommend" :key="index">{{ item }}</span>
              </div>
              <div class="time">{{ rating.rateTime | formatDate }}</div>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
import star from 'components/star/star'
import split from 'components/split/split'
import ratingselect from 'components/ratingselect/ratingselect'
import BScroll from 'better-scroll'
import {formatDate} from 'common/js/date'

const ALL = 2
const debug = process.env.NODE_ENV !== 'production'
const ERR_OK = 0

export default {
  props: {
    seller: {
      type: Object
    }
  },
  data () {
    return {
      ratings: [],
      selectType: ALL,
      onlyContent: true
    }
  },
  created () {
    const url = debug ? '/api/ratings' : 'http://ustbhuangyi.com/sell'
    this.$http.get(url).then((response) => {
      response = response.body
      if (response.errno === ERR_OK) {
        this.ratings = response.data
        this.$nextTick(function () {
          if (!this.scroll) {
            this.scroll = new BScroll(this.$refs.ratings, { click: true })
          } else {
            this.scroll.refresh()
          }
        })
      }
    })
  },
  methods: {
    selectRating (type) {
      this.selectType = type
      this.$nextTick(() => {
        // 选择后刷新
        this.scroll.refresh()
      })
    },
    toggleContent () {
      this.onlyContent = !this.onlyContent
      this.$nextTick(() => {
        this.scroll.refresh()
      })
    },
    needShow (type, text) {
      if (this.onlyContent && !text) {
        return false
      }
      if (this.selectType === ALL) {
        return true
      } else {
        return type === this.selectType
      }
    }
  },
  filters: {
    formatDate (time) {
      let date = new Date(time)
      return formatDate(date, 'yyyy-MM-dd-hh:mm')
    }
  },
  components: {
    star,
    split,
    ratingselect
  }
}
</script>

<style lang="stylus">
@import "../../common/stylus/mixin.styl"
.ratings
  position: absolute
  top: 174px
  bottom: 0
  left: 0
  width: 100%
  overflow: hidden
  z-index: 30
  .overview
    display: flex
    padding: 18px 0
    .overview_left
      flex 0 0 137px
      width: 137px
      padding: 6px 0
      text-align: center
      border-right: 1px solid rgba(7,17,27,.1)
      @media only screen and (max-width: 320px)
        flex: 0 0 120px
      .score
        font-size: 24px
        color: rgb(255,153,0)
        line-height: 28px
        margin-bottom: 6px
      .title
        margin-bottom: 8px
        font-size: 12px
        line-height: 12px
        color: rgb(7,17,27)
      .rank
        line-height: 10px
        color: rgb(147,153,159)
        font-size: 10px
    .overview_right
      flex: 1
      padding: 6px 0px 6px 24px
      @media only screen and (max-width: 320px)
        margin-left: 6px
      .score-wrapper
        font-size: 0
        .title
          font-size: 12px
          display: inline-block
          line-height: 18px
          vertical-align: top
          color: rgb(7,17,27)
        .star
          display: inline-block
          line-height: 18px
          vertical-align: top
          margin: 0 12px
        .score
          font-size: 12px
          display: inline-block
          line-height: 18px
          vertical-align: top
          color: rgb(255,153,0)
      .delivery-wrapper
        font-size: 0
        .title
          font-size: 12px
          color: rgb(7,17,27)
          line-height: 18px
          margin-right: 12px
        .delivery
          font-size: 12px
          color: rgb(147,153,159)
          line-height: 18px
  .rating_wrapper
    padding: 0 18px
    .rating_item
      display: flex
      padding: 18px 0
      border-1px(rgba(7,17,27,.1))
      .avatar
        flex 0 0 28px
        width: 28px
        img
          border-radius: 50%
      .content
        position:relative
        margin-left: 12px
        flex: 1
        .name
          font-size: 10px
          color: rgb(7,17,27)
          line-height: 12px
          margin-bottom: 4px
        .star-wrapper
          margin-bottom: 6px
          font-size: 0
          .star
            display: inline-block
            margin-right: 6px
            vertical-align: top
          .delivery
            display: inline-block
            vertical-align: top
            line-height: 12px
            font-weight: 200
            color: rgb(147,153,159)
        .text
          margin-bottom: 8px
          line-height: 18px
          color: rgb(7,17,27)
          font-size: 12px
        .recommend
          line-height: 16px
          font-size: 0px
          .icon-thumb_up, .icon-thumb_down
            display: inline-block
            line-height: 24px
            font-size: 9px
          .icon-thumb_up
            color: rgb(0,160,220)
          .icon-thumb_down
            color: rgb(147,153,159)
          .item
            padding: 0 6px
            border: 1px solid rgba(7,17,27,.1)
            color: rgb(147,153,159)
            border-radius: 1px
            background: #ffffff
        .time
          position: absolute
          line-height: 12px
          top: 0
          right: 0
          font-size: 10px
          font-weight: 200
          color: rgb(147,153,159)
</style>
