<template>
  <div>
    <div class="goods">
      <div class="menu_wrapper" ref="menuWrapper">
        <ul>
          <li v-for="(item,index) in goods"
            class="menu_item"
            :class="{'current': currentIndex === index}"
            :key="index"
            ref="menuList"
            @click="selectMenu(index)">
            <span class="text">
              <span class="icon"
                v-show="item.type>0"
                :class="classMap[item.type]"></span>
              {{ item.name }}
            </span>
          </li>
        </ul>
      </div>
      <!--==========================-->
      <div class="foods-wrapper" ref="foodsWrapper">
        <ul>
          <li v-for="(item,index) in goods" class="food-list" :key="index" ref="foodList">
            <h1 class="title">{{ item.name }}</h1>
            <ul>
              <li v-for="(food,foodIndex) in item.foods" :key="foodIndex" class="food-item border-1px">
                <div class="icon" @click="selectFood(food)">
                  <img :src="food.icon" height="57" width="57"/>
                </div>
                <!--=======================-->
                <div class="content">
                  <h2 class="name">{{ food.name }}</h2>
                  <p class="desc">{{ food.description }}</p>
                  <div class="extra">
                    <span class="count">月售{{ food.sellCount }}份</span><span>好评率{{ food.rating }}%</span>
                  </div>
                  <!--======================-->
                  <div class="price">
                    <span class="now">￥{{ food.price }}</span><span class="old" v-show="food.oldPrice">￥{{ food.oldPrice }}</span>
                  </div>
                  <!--======================-->
                  <div class="cartcontrol-wrapper">
                    <cartcontrol :food="food" @add="addFood"></cartcontrol>
                  </div>
                </div>
              </li>
            </ul>
          </li>
        </ul>
      </div>
      <!--购物车组件-->
      <shopcart class="shopcart" :deliveryPrice="seller.deliveryPrice" :minPrice="seller.minPrice" :sellectFoods="sellectFoods" ref="shopcart"></shopcart>
      <!--食物详情页-->
      <food ref="food" :food="selectedFood" @add="addFood"></food>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
import BScroll from 'better-scroll'
import cartcontrol from 'components/cartcontrol/cartcontrol'
import shopcart from 'components/shopcart/shopcart'
import food from 'components/food/food'
const ERR_OK = 0
const debug = process.env.NODE_ENV !== 'production'
export default {
  props: {
    food: {
      type: Object
    },
    seller: {
      type: Object
    }
  },
  data () {
    return {
      goods: [],
      listHeight: [],
      scrollY: 0,
      selectedFood: {}
    }
  },
  created () {
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
    const url = debug ? '/api/goods' : 'http://ustbhuangyi.com/sell/api/goods'
    this.$http.get(url)
      .then((response) => {
        response = response.body
        // console.log(response)
        if (response.errno === ERR_OK) {
          this.goods = response.data
          // 保证先刷新页面，然后再进行计算
          this.$nextTick(() => {
            // 初始化better-scroll对象
            this._initScroll()
            this._calculateHeight()
          })
        }
      })
  },
  methods: {
    _initScroll () {
      this.menuScroll = new BScroll(this.$refs.menuWrapper, { click: true })
      this.foodsScroll = new BScroll(this.$refs.foodsWrapper, {
        probeType: 3,
        click: true
      })
      this.foodsScroll.on('scroll', (pos) => {
        if (pos.y <= 0) {
          this.scrollY = Math.abs(Math.round(pos.y))
        }
      })
    },
    _calculateHeight () {
      let foodList = this.$refs.foodList
      let height = 0
      this.listHeight.push(height)
      for (let i = 0; i < foodList.length; i++) {
        let item = foodList[i]
        height += item.clientHeight
        this.listHeight.push(height)
      }
    },
    _followScroll (index) {
      let menuList = this.$refs.menuList
      let el = menuList[index]
      /* 参数1 滚动的元素
       * 参数2 滚动时间
       * 参数3 起始的起点和终点
       * */
      this.menuScroll.scrollToElement(el, 300, 0, -100)
    },
    selectMenu (index) {
      let foodList = this.$refs.foodList
      let el = foodList[index]
      this.foodsScroll.scrollToElement(el, 100)
    },
    addFood (target) {
      this._drop(target)
    },
    _drop (target) {
      this.$refs.shopcart.drop(target)
    },
    selectFood (food) {
      this.selectedFood = food
      this.$refs.food.show()
    }
  },
  computed: {
    currentIndex () {
      for (let i = 0; i < this.listHeight.length; i++) {
        let height1 = this.listHeight[i]
        let height2 = this.listHeight[i + 1]
        if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
          this._followScroll(i)
          return i
        }
      }
      return 0
    },
    sellectFoods () {
      let foods = []
      this.goods.forEach((good) => {
        good.foods.forEach((food) => {
          if (food.count) {
            foods.push(food)
          }
        })
      })
      return foods
    }
  },
  components: {
    cartcontrol,
    shopcart,
    food
  }
}
</script>

<style lang="stylus" rel="stylesheet/stylus">
@import "../../common/stylus/mixin.styl";
.goods
  display: flex
  position: absolute
  top: 174px
  bottom: 46px
  width: 100%
  overflow: hidden
  .menu_wrapper
    flex: 0 0 80px
    width: 80px
    background: #f3f5f7
    .menu_item
      display: table
      height: 54px
      width: 56px
      padding: 0 12px
      line-height: 14px
      &.current
        position: relative
        z-index: 10
        margin-top: -1px
        font-weight: 700
        background: #ffffff
        .text
          border-none()
      .text
        display: table-cell
        width: 56px
        vertical-align: middle
        border-1px(rgba(7,17,27,0.1))
        font-size: 12px
        .icon
          display: inline-block
          width: 12px
          height: 12px
          margin-right: 2px
          background-size: 12px 12px
          background-repeat: no-repeat
          &.decrease
            bg-image('decrease_3')
          &.discount
            bg-image('discount_3')
          &.special
            bg-image('special_3')
          &.invoice
            bg-image('invoice_3')
          &.guarantee
            bg-image('guarantee_3')
  .foods-wrapper
    flex: 1
    .title
      padding-left: 14px
      height: 26px
      line-height: 26px
      border-left: 2px solid #d9dde1
      font-size: 12px
      color: rgb(147,153,159)
      background: #f3f5f7
    .food-item
      display: flex
      margin: 18px
      padding-bottom: 18px
      border-1px(rgba(7,17,27,0.1))
      &:last-child
        border-none()
        margin-bottom: 0
      .icon
        flex: 0 0 57px
        margin-right: 10px
      .content
        flex: 1
        .name
          margin: 2px 0 8px 0
          height: 14px
          line-height: 14px
          font-size: 14px
          color: rgb(7,17,27)
        .desc,.extra
          line-height: 10px
          font-size: 10px
          color: rgb(147,153,159)
        .desc
          margin-bottom: 8px
        .extra
          .count
            margin-right: 12px
        .price
          font-weight: 700
          line-height: 24px
          .now
            margin-right: 8px
            font-size: 14px
            color: rgb(240,20,20)
          .old
            text-decoration: line-through
            font-size: 10px
            color: rgb(147,153,159)
        .cartcontrol-wrapper
          position: absolute
          bottom: 11.5px
          right: 0
</style>
