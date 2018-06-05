<template>
  <div id="app">
    <v-header :seller="seller"></v-header>
    <div class="tab border-1px">
      <div class="tab-item">
        <router-link to="/goods">商品</router-link>
      </div>
      <div class="tab-item">
        <router-link to="/ratings">评论</router-link>
      </div>
      <div class="tab-item">
        <router-link to="/seller">商家</router-link>
      </div>
    </div>
    <router-view :seller="seller" />
  </div>
</template>

<script type="text/ecmascript-6">
import vHeader from '@/components/header/header'
export default {
  data () {
    return {
      seller: {
      }
    }
  },
  created () {
    this.$http.get('/api/seller')
      .then((response) => {
        // console.log(response)
        response = response.body
        // console.log(response)
        this.seller = response.data
      })
  },
  components: {
    vHeader
  }
}
</script>

<style lang="stylus" rel="stylesheet/stylus">
@import "./common/stylus/mixin.styl"
.tab
  display: flex
  width: 100%
  height: 40px
  line-height: 40px

  .tab-item
    flex: 1
    text-align: center

    & > a
      display: block
      color: rgb(77,85,93)
      font-size: 14px
      &.active
        color: rgb(240,20,20)
</style>
