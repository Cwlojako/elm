<template>
  <div class="ratings">
    <div class="ratings-header">
      <div class="score-left">
          <div class="number">{{seller.score}}</div>
          <div class="text">综合评分</div>
          <div class="rankRate">高于周边商家{{seller.rankRate}}</div>
      </div>
      <div class="stars">
        <div class="serviceScore star-right">
          <span class="text">服务态度</span>
          <div class="star-wrapper">
            <star :size="36" :score="seller.serviceScore"></star>
          </div>
          <span class="score">{{seller.serviceScore}}</span>
        </div>
        <div class="foodScore star-right">
          <span class="text">餐品评价</span>
          <div class="star-wrapper">
            <star :size="36" :score="seller.foodScore"></star>
          </div>
          <span class="score">{{seller.foodScore}}</span>
        </div>
        <div class="deliveryTime star-right">
          <span class="text">送达时间</span><span class="time">{{seller.deliveryTime}}分钟</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import star from '@/components/star/star.vue'
  export default {
    props: {
      seller: {
        type: Object
      }
    },
    components: {
      star
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import  '../../common/stylus/mixin.styl'
  .ratings
    .ratings-header
      display: flex
      .score-left
        flex: 0 0 137px
        margin: 18px 20px
        padding-right: 0
        border-1px-right(rgba(147,153,159,0.4))
        .number
          font-size: 24px
          color: rgb(255,153,0)
          line-height: 28px
          text-align: center
          margin-bottom: 6px
        .text
          font-size: 12px
          color: rgb(7,17,27)
          line-height: 12px
          text-align: center
          margin-bottom: 8px
        .rankRate
          font-size: 10px
          margin-bottom: 6px
          color: rgb(147,153,159)
          line-height: 10px
          text-align: center
      .stars
        flex: 1
        padding: 18px 10px 0 0 
        .star-right
          font-size: 0
          margin-bottom: 8px
          &:last-child
            margin-bottom: 0
          .text
            display: inline-block
            vertical-align: top
            font-size: 12px
            line-height: 18px
            margin-right: 6px
          .star-wrapper
            display: inline-block
            vertical-align: top
            padding-top: 1px
            margin-right: 6px
          .score
            display: inline-block
            vertical-align: top
            font-size: 12px
            color: rgb(255,153,0)
            line-height: 18px
          .time
            font-size: 12px
            line-height: 18px
            color: rgb(147,153,159)
</style>
