<template>
  <div class="header">
    <div class="content-wrapper">
      <div class="avatar">
        <img :src="seller.avatar" width="64" height="64"/>
      </div>
      <div class="content">
        <div class="title">
          <span class="brand"></span>
          <span class="name">{{ seller.name }}</span>
        </div>
        <!--======================================-->
        <div class="description">
          {{ seller.description}}/{{seller.deliveryTime}}分钟送达
        </div>
        <!--======================================-->
        <div class="support" v-if="seller.supports">
          <span class="icon" :class="classMap[seller.supports[0].type]"></span>
          <span class="text">{{ seller.supports[0].description }}</span>
        </div>
      </div>
      <div class="support-count" v-if="seller.supports" @click="showDetail">
        <span class="count">{{ seller.supports.length }}个</span>
        <i class="icon-keyboard_arrow_right"></i>
      </div>
    </div>
    <div class="bulletin-wrapper" @click="showDetail">
       <span class="bulletin-title"></span><span class="bulletin-text">{{ seller.bulletin }}</span>
       <i class="icon-keyboard_arrow_right"></i>
    </div>
    <div class="background">
      <img :src="seller.avatar" width="100%" height="100%"/>
    </div>
    <transition>
      <div class="detail" v-show="detailShow">
        <div class="detail-wrapper clearfix">
          <div class="detail-main">
            <h1 class="name">{{ seller.name }}</h1>
            <div class="star-wrapper">
              <star :size="48" :score="seller.score"></star>
            </div>
            <div class="title">
              <div class="line"></div>
              <div class="text">优惠信息</div>
              <div class="line"></div>
            </div>
            <ul v-if="seller.supports" class="supports">
              <li class="supports-item" v-for="(item,index) in seller.supports" :key="index">
                <span class="icon" :class="classMap[seller.supports[index].type]"></span>
                <span class="text">{{ item.description }}</span>
              </li>
            </ul>
            <div class="title">
              <div class="line"></div>
              <div class="text">商家公告</div>
              <div class="line"></div>
            </div>
            <div class="bulletin">
              <div class="content">{{ seller.bulletin }}</div>
            </div>
          </div>
          <div class="detail-close" @click="hideDetail">
            <i class="icon-close"></i>
          </div>
        </div>
      </div>
    </transition>

  </div>
</template>

<script type="text/ecmascript-6">
import star from 'components/star/star'
export default {
  props: {
    seller: {
      type: Object
    }
  },
  data () {
    return {
      detailShow: false
    }
  },
  created () {
    this.classMap = [ 'decrease', 'discount', 'special', 'invoice', 'guarantee' ]
  },
  methods: {
    showDetail () {
      this.detailShow = true
    },
    hideDetail () {
      this.detailShow = false
    }
  },
  components: {
    star
  }
}
</script>

<style lang="stylus" rel="stylesheet/stylus">
@import "../../common/stylus/mixin.styl";
  .header
    position: relative
    background: rgba(7,17,27,0.5)
    color: #ffffff
    .content-wrapper
      position: relative
      padding: 24px 12px 18px 24px
      .avatar
        display: inline-block
        img
          border-radius: 2px
      .content
        display: inline-block
        margin-left: 11px
        .title
          margin: 2px 0 8px 0
          .brand
            display: inline-block
            bg-image('brand')
            width: 30px
            height: 18px
            background-size: 30px 18px
            background-repeat: no-repeat
            vertical-align: text-bottom
          .name
            font-size: 16px
            color: rgb(255,255,255)
            font-weight: bold
            line-height: 18px
        .description
          margin-bottom: 10px
          font-size: 12px
          line-height: 12px
        .support
          .icon
            display: inline-block
            width: 12px
            height: 12px
            margin-right: 4px
            background-size: 12px 12px
            background-repeat: no-repeat
            vertical-align: middle
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
            font-size: 10px
            line-height: 12px
      .support-count
        background: rgba(0,0,0,0.2)
        border-radius: 20px
        position: absolute
        right: 12px
        bottom: 14px
        line-height: 24px
        padding: 0px 8px
        text-align: center
        .count
          font-size: 10px
          vertical-align: bottom
        .icon-keyboard_arrow_right
          font-size: 10px
    .bulletin-wrapper
      padding: 0px 20px 0 12px
      position: relative
      background: rgba(7,17,27,0.2)
      height: 28px
      line-height: 28px
      overflow: hidden
      white-space: nowrap
      text-overflow: ellipsis
      .bulletin-title
        display: inline-block
        width: 22px
        height: 12px
        background-size: 22px 12px
        background-repeat: no-repeat
        bg-image('bulletin')
        vertical-align: middle
      .bulletin-text
        font-size: 10px
        margin: 0px 4px
      .icon-keyboard_arrow_right
        position: absolute
        right: 8px
        top: 8px
    .background
      position: absolute
      width: 100%
      height: 100%
      top: 0
      left: 0
      z-index: -1
      filter: blur(10px)
    .detail
      position: fixed
      z-index: 100
      top: 0
      left: 0
      width: 100%
      height: 100%
      overflow: auto
      backdrop-filter: blur(10px)
      opacity: 1
      background: rgba(7,17,27,0.8)
      &.fade-enter-active,&.fade-leave-active
        transition: all 0.5s
      &.fade-enter,&.fade-leave-active
        opacity: 0
        background: rgba(7,17,27,0)
      .detail-wrapper
        min-height: 100%
        width: 100%
        .detail-main
          margin-top: 64px
          padding-bottom: 64px
          .name
            line-height: 16px
            text-align: center
            font-size: 16px
            font-weight: 700
          .star-wrapper
            margin-top: 16px
            text-align: center
          .title
            display: flex
            font-size: 14px
            font-weight: 700
            width: 80%
            margin: 28px auto 24px
            .line
              flex: 1
              position: relative
              top: -6px
              border-bottom: 2px solid rgba(255,255,255,0.2)
            .text
              margin: 0 12px
          .supports
            width: 80%
            margin 0 auto
            .supports-item
              margin-left: 12px
              margin-bottom: 12px
              .icon
                display: inline-block
                width: 16px
                height: 16px
                margin-right: 6px
                background-size: 16px 16px
                background-repeat: no-repeat
                vertical-align: middle
                &.decrease
                  bg-image('decrease_2')
                &.discount
                  bg-image('discount_2')
                &.special
                  bg-image('special_2')
                &.invoice
                  bg-image('invoice_2')
                &.guarantee
                  bg-image('guarantee_2')
              .text
                font-size: 12px
                line-height: 12px
          .bulletin
            width: 80%
            margin: 0 auto
            .content
              font-size: 12px
              line-height: 24px
              padding: 0 12px

        .detail-close
          position: relative
          width: 32px
          height: 32px
          font-size: 32px
          clear: both
          margin: 0 auto
</style>
