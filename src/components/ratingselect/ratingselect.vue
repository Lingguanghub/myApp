<template>
  <div class="ratingselect">
    <div class="rating-type border-1px">
      <span class="block positive" :class="{'active':selectType === 2}" @click="select(2)">{{ desc.all }}
        <span class="count">{{ ratings.length }}</span>
      </span>
      <span class="block positive" :class="{'active':selectType === 0}" @click="select(0)">{{ desc.positive }}
        <span class="count">{{ positives.length }}</span>
      </span>
      <span class="block negative" :class="{'active':selectType === 1}" @click="select(1)">{{ desc.negative }}
        <span class="count">{{ negatives.length }}</span>
      </span>
    </div>
    <!--=========================================-->
    <div class="switch" :class="{'on':onlyContent}" @click="toggleContent">
      <span class="icon-check_circle"></span>
      <span class="text">只看有内容的评价</span>
    </div>
  </div>
</template>

<script>
const ALL = 2
const POSITIVE = 0
const NAGATIVE = 1
export default {
  props: {
    ratings: {
      type: Array,
      default () {
        return []
      }
    },
    selectType: {
      type: Number,
      default: ALL
    },
    onlyContent: {
      type: Boolean,
      default: false
    },
    desc: {
      type: Object,
      default () {
        return {
          all: '全部',
          positive: '满意',
          negative: '不满意'
        }
      }
    }
  },
  computed: {
    positives () {
      return this.ratings.filter((rating) => {
        return rating.rateType === POSITIVE
      })
    },
    negatives () {
      return this.ratings.filter((rating) => {
        return rating.rateType === NAGATIVE
      })
    }
  },
  methods: {
    select (type) {
      this.$emit('select', type)
    },
    toggleContent () {
      this.$emit('toggle')
    }
  }
}
</script>

<style lang="stylus">
@import "../../common/stylus/mixin.styl"
.ratingselect
  .rating-type
    padding: 18px 0
    margin: 0 18px
    border-1px(rgba(7,17,27,.1))
    font-size: 0
    .block
      display: inline-block
      padding: 8px 12px
      margin-right: 8px
      line-height: 12px
      font-size: 12px
      border-radius: 1px
      color: rgb(77,85,93)
      &.active
        color: #fff
        .count
          margin-left: 2px
          font-size: 8px
      &.positive
        background: rgba(0,160,220,.2)
        &.active
          background: rgb(0,160,220)
      &.negative
        background: rgba(77,85,93,.2)
        &.active
          background: rgb(77,85,93)
  .switch
    padding: 12px 8px
    line-height: 24px
    border-bottom: 1px solid rgba(7,17,27,.1)
    color: rgb(147,153,159)
    font-size: 0
    &.on
      .icon-check_circle
        color: #00c850
    .icon-check_circle
      display: inline-block
      margin-right: 4px
      font-size: 24px
      vertical-align: top
    .text
      display: inline-block
      font-size: 12px
      vertical-align: top
</style>
