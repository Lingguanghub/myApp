<template>
  <div>
    <div class="shopcart">
      <div class="content" @click="toggleList">
        <div class="content_left">
          <div class="logo-wrapper">
            <div class="logo" :class="{'highlight':totalCount>0}">
              <i class="icon-shopping_cart" :class="{'highlight':totalCount>0}"></i>
            </div>
            <div class="num" v-show="true" :class="{'highlight':totalCount>0}">{{ totalCount }}</div>
          </div>
          <!--================================-->
          <div class="price">¥{{ totalPrice }}</div>
          <!--=============================-->
          <div class="desc">另需配送费¥{{ deliceryPrice }}</div>
        </div>
        <div class="content_right" @click.stop.prevent="pay">
          <div class="pay" :class="payClass">{{ payDesc }}</div>
        </div>
      </div>
      <div class="ball-container">
        <div v-for="(ball,index) in balls" :key="index">
          <transition name="drop"
            @before-enter="beforeDrop"
            @enter="dropping"
            @after-enter="afterDrop">
            <div class="ball" v-show="ball.show">
              <div class="inner inner-hook"></div>
            </div>
          </transition>
        </div>
      </div>
      <transition name="fold">
        <div class="shopcart-list" v-show="listShow">
          <div class="list-header">
            <h1 class="title">购物车</h1>
            <span class="empty" @click="empty">清空</span>
          </div>
          <div class="list-content" ref="listContent">
            <ul>
              <li class="food" v-for="(food,index) in sellectFoods" :key="index">
                <span>{{ food.name }}</span>
                <div class="price">
                  <span>¥{{ food.price * food.count }}</span>
                </div>
                <div class="cartcontrol-wrapper">
                  <cartcontrol :food="food" @add="addFood"></cartcontrol>
                </div>
              </li>
            </ul>
          </div>
        </div>
      </transition>
      <!--=====================================-->
      <!--购物车北京浮层-->
      <transition name="fade">
        <div class="list-mask" v-show="listShow" @click="hideList">
        </div>
      </transition>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
import cartcontrol from 'components/cartcontrol/cartcontrol'
import BScorll from 'better-scroll'
import Vue from 'vue'
export default {
  props: {
    deliceryPrice: {
      type: Number,
      default: 0
    },
    minPrice: {
      type: Number,
      default: 0
    },
    sellectFoods: {
      type: Array,
      default () {
        return [{
          price: 10,
          count: 1
        }]
      }
    },
    food: {
      type: Object
    }
  },
  data () {
    return {
      balls: [
        {
          show: false
        },
        {
          show: false
        },
        {
          show: false
        },
        {
          show: false
        },
        {
          show: false
        }
      ],
      dropBalls: [],
      fold: true,
      scroll: false
    }
  },
  computed: {
    payClass () {
      if (this.totalPrice < this.minPrice) {
        return 'not-enough'
      } else {
        return 'enough'
      }
    },
    totalCount () {
      let count = 0
      this.sellectFoods.forEach((food) => {
        count += food.count
      })
      return count
    },
    totalPrice () {
      let total = 0
      this.sellectFoods.forEach((food) => {
        total += food.price * food.count
      })
      return total
    },
    payDesc () {
      if (this.totalPrice === 0) {
        return `¥${this.minPrice}元起送`
      } else if (this.totalPrice < this.minPrice) {
        let diff = this.minPrice - this.totalPrice
        return `还差¥${diff}元起送`
      } else {
        return '去结算'
      }
    },
    listShow () {
      if (!this.totalCount) {
        // 在Vue中getter函数是没有副作用的，但是在setter函数是有副作用的
        Vue.set(this, 'fold', true)
        return false
      }
      let show = !this.fold
      if (show) {
        this.$nextTick(() => {
          // better-scroll的初始化
          if (!this.scroll) {
            let betterScroll = new BScorll(this.$refs.listContent, {
              click: true
            })
            if (betterScroll) {
              Vue.set(this, 'scroll', true)
            } else {
              Vue.set(this, 'scroll', false)
            }
          }
        })
      }
      return show
    }
  },
  methods: {
    drop (el) {
      for (let i = 0; i < this.balls.length; i++) {
        let ball = this.balls[i]
        if (!ball.show) {
          ball.show = true
          ball.el = el
          // console.log(ball.el)
          this.dropBalls.push(ball)
          return
        }
      }
    },
    // 小球抛出之前的定位：把小球放到【+号】处
    beforeDrop (el) {
      // console.log(el)
      let count = this.balls.length
      while (count--) {
        let ball = this.balls[count]
        if (ball.show) {
          let rect = ball.el.getBoundingClientRect()
          let x = rect.left - 22
          let y = -(window.innerHeight - rect.top - 22)
          el.style.display = ''
          el.style.webkitTransform = `translate3d(0, ${y}px, 0)`
          el.style.transform = `translate3d(0, ${y}px, 0)`
          let inner = el.getElementsByClassName('inner-hook')[0]
          inner.style.webkitTransform = `translate3d(${x}px, 0, 0)`
          inner.style.transform = `translate3d(${x}px, 0, 0)`
        }
      }
    },
    // 抛出中
    dropping (el, done) {
      /* eslint-disable no-unused-vars */
      let rf = el.offsetHeight
      this.$nextTick(() => {
        el.style.webkitTransform = 'translate3d(0,0,0)'
        el.style.transform = 'translate3d(0,0,0)'
        let inner = el.getElementsByClassName('inner-hook')[0]
        inner.style.webkitTransform = 'translate3d(0,0,0)'
        inner.style.transform = 'translate3d(0,0,0)'
        el.addEventListener('transitionend', done)
      })
    },
    // 抛球完成
    afterDrop (el) {
      let ball = this.dropBalls.shift()
      if (ball) {
        ball.show = false
        el.style.display = 'none'
      }
    },
    toggleList () {
      if (!this.totalCount) {
        return
      }
      this.fold = !this.fold
    },
    addFood (target) {
      this.drop(target)
    },
    empty () {
      this.sellectFoods.forEach((food) => {
        food.count = 0
      })
    },
    // 点击背景的位置可以隐藏浮层
    hideList () {
      this.fold = true
    },
    pay () {
      if (this.totalPrice < this.minPrice) {
        return
      }
      window.alert(`已经从你的账户扣除${this.totalPrice}元`)
    }
  },
  components: {
    cartcontrol
  }
}
</script>

<style lang="stylus" rel="stylesheet/stylus">
@import "../../common/stylus/mixin.styl"
.shopcart
  position: fixed
  left: 0
  bottom: 0
  width: 100%
  height: 46px
  z-index:44
  .content
    display: flex
    background: #141d27
    color: rgba(255,255,255,.4)
    .content_left
      flex: 1
      .logo-wrapper
        display: inline-block
        position: relative
        padding: 6px
        border-radius: 50%
        background: #141d27
        height: 56px
        width: 56px
        margin: 0 16px
        box-sizing: border-box
        margin-top: -10px
        .logo
          background: rgba(255,255,255,.2)
          width: 100%
          height: 100%
          border-radius: 50%
          text-align: center
          &.highlight
            background: rgb(0,160,220)
          .icon-shopping_cart
            line-height: 44px
            font-size: 24px
            color: #80858a
            &.highlight
              color: #ffffff
        .num
          position: absolute
          top: 0
          right: 0
          width: 24px
          height: 16px
          line-height: 16px
          color: #ffffff
          font-weight: 700
          background: rgb(240,20,20)
          border-radius: 12px
          text-align: center
          box-shadow: 0px 4px 8px 0 rgba(0,0,0,.4)
          font-size: 9px
      .price
        display: inline-block
        font-size: 16px
        font-weight: 700
        border-right: 1px solid rgba(255,255,255,.1)
        padding-right: 12px
        padding-top: 4px
        padding-bottom: 4px
        &.highlight
          background: #ffffff
      .desc
        display: inline-block
        margin-left: 12px
        font-weight: 700
        font-size: 16px
        line-height: 24px
        color: rgba(255,255,255,.4)

    .content_right
      flex: 0 0 105px
      .pay
        font-size: 12px
        color: rgba(255,255,255,.4)
        font-weight: 700
        line-height: 46px
        background: rgba(255,255,255,0.2)
        text-align: center
        height: 100%
        &.not-enough
          background: #2b33b
        &.enough
          background: #00b43c
          color: #ffffff
  .ball-container
    .ball
      position: fixed
      left: 32px
      bottom: 22px
      z-index: 200
      transition: all 0.4s cubic-bezier(.49,-0.29,.75,.41)
      .inner
        width: 16px
        height: 16px
        border-radius: 50%
        background: rgb(0,160,220)
        transition: all 0.4s linear
  .shopcart-list
    position: absolute
    left: 0
    top: 0
    z-index: -1
    width: 100%
    transform: translate3d(0,-100%,0)
    &.fold-enter-active, &.fold-leave-active
      transition: all 0.5s
    &.fold-enter, &.fold-leave-active
      transform: translate3d(0,0,0)
    .list-header
      height: 40px
      line-height: 40px
      padding: 0 18px
      background: #f3f5f7
      border-bottom: 1px solid rgba(7,17,27,0.1)
      .title
        float: left
        font-size: 14px
        color: rgb(7,17,27)
      .empty
        float: right
        font-size: 12px
        color: rgb(0,160,220)
    .list-content
      padding: 018px
      max-height: 258px
      overflow: hidden
      background: #fff
      .food
        position: relative
        padding: 12px 0
        box-sizing: border-box
        border-1px(rgba(7,17,27,.1))
        .name
          line-height: 24px
          font-size: 14px
          color: rgb(7,17,27)
        .price
          position: absolute
          right: 90px
          bottom: 12px
          line-height: 24px
          font-size: 14px
          font-weight: 700
          color: rgb(240,20,20)
        .cartcontrol-wrapper
          position: absolute
          bottom: 6px
          right: 0
  .list-mask
    position: fixed
    top: 0
    left: 0
    z-index: -2
    width: 100%
    height: 100%
    background: rgba(7,17,27,.6)
    opacity: 1
    &.fade-enter-active,&.fade-leave-active
      opacity: 0
      background: rgba(7,17,27,0)
</style>
