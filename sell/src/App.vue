<template>
  <div id="app">
    <v-header :seller="seller"></v-header>
    <div class="tag border-1px">
        <div class="tag-item">
          <router-link to='/goods'>商品</router-link>
        </div>
      <div class="tag-item">
        <router-link to='/ratings'>评论</router-link>
      </div>
      <div class="tag-item">
        <router-link to='/seller'>商家</router-link>
      </div>
    </div>
    <router-view :seller="seller"></router-view>
  </div>
</template>

<script type='text/ecmascript-6'>
import header from '@/components/header/header.vue'

const ERR_OK = 0

export default {
  data() {
    return {
      seller: {}
    }
  },
  created() {
    this.$http.get('/api/seller').then((response) => {
      response = response.body
      if (response.errno === ERR_OK) {
        this.seller = response.data
        console.log(this.seller)
      }
    })
  },
  components: {
    'v-header': header
  }
}
</script>

<style lang="stylus" rel="stylesheet/stylus">
@import './common/stylus/mixin'
    #app
      .tag
        display : flex
        width : 100%
        height : 40px
        line-height : 40px
        border-1px(rgba(7,17,27,0.1))
        .tag-item
          flex : 1
          text-align : center
          .active
           color : rgb(240,20,20)
          a
           font-size : 14px
           display : block
           color : rgb(77,85,93)
</style>
