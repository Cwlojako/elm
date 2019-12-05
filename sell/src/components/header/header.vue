<template>
  <div class="header">
    <div class="content-wrapper">
      <div class="avatar">
        <img height="64" width="64" :src="seller.avatar"/>
      </div>
      <div class="content">
        <div class="title">
          <span class="brand"></span>
          <span class="name">{{seller.name}}</span>
        </div>
        <div class="description">
          <span>{{seller.description}}/{{seller.deliveryTime}}分钟送达</span>
        </div>
        <div class="support" v-if="seller.supports">
          <span class="icon" :class="classMap[seller.supports[0].type]"></span>
          <span class="text">{{seller.supports[0].description}}</span>
        </div>
      </div>
      <div class="support-count" v-if="seller.supports" @click="showDetail">
        <span class="count">{{seller.supports.length}}个</span>
        <i class="icon-keyboard_arrow_right"></i>
      </div>
    </div>
    <div class="bulletin-wrapper">
      <span class="bulletin-icon"></span><span class="bulletin-content">{{seller.bulletin}}</span>
      <i class="icon-keyboard_arrow_right" @click="showDetail"></i>
    </div>
    <div class="header-background">
      <img :src="seller.avatar" width="100%" height="100%"/>
    </div>
    <transition name="fade">
      <div class="detail" v-show="detailShow">
        <div class="detail-wrapper clearfix">
          <div class="detail-main">
            <div class="detail-title">
              <p>{{seller.name}}</p>
            </div>
            <div class="detail-star">
              <star :size="48" :score="seller.score"></star>
            </div>
            <div class="yh-title detail-tips">
              <div class="line"></div>
              <div class="text">优惠信息</div>
              <div class="line"></div>
            </div>
            <div v-if="seller.supports" class="yh-out">
              <div class="yh-items" v-for="(item,index) in seller.supports" :key="index">
                  <span class="yh-icon" :class="classMap[seller.supports[index].type]"></span>
                  <span class="yh-text">{{item.description}}</span>
              </div>
            </div>
            <div class="bulletin-title detail-tips">
              <div class="line"></div>
              <div class="text">商家公告</div>
              <div class="line"></div>
            </div>
            <div class="bulletin-out">
              <p class="text">{{seller.bulletin}}</p>
            </div>
          </div>
        </div>
        <div class="detail-close">
          <i class="icon-close" @click="showDetail"></i>
        </div>
      </div>
    </transition>
  </div>
</template>

<script type="text/ecmascript-6">
  import star from '../star/star.vue'
  export default {
    props: {
      seller: {
        type: Object
      }
    },
    data() {
      return {
        detailShow: false
      }
    },
    methods: {
      showDetail() {
        this.detailShow = !this.detailShow
      }
    },
    created() {
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
    },
    components: {
      star
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
@import '../../common/stylus/mixin.styl'
  .header
    position: relative
    background-color: rgba(7,17,27,0.5)
    overflow: hidden
    .bulletin-wrapper
      position: absolute
      bottom: 0
      left: 0
      height: 28px
      line-height: 28px
      width: 100%
      padding: 0 22px 0 12px
      background-color: rgba(0,0,0,0.3)
      color: rgb(255,255,255)
      box-sizing: border-box
      overflow: hidden
      text-overflow:ellipsis
      white-space: nowrap
      z-index: 1
      .bulletin-icon
        display: inline-block
        vertical-align: middle
        width: 22px
        height: 12px
        bg-image('./img/bulletin')
        background-size: 22px 12px
      .bulletin-content
        padding: 0 4px
        font-size: 10px
        font-weight: 200
        line-height: 28px
      .icon-keyboard_arrow_right
        position: absolute
        right: 12px
        top: 11px
        font-size: 10px
    .content-wrapper
      padding: 24px 0 40px 28px
      position: relative
      z-index: 1
      overflow : auto
      box-sizing : border-box
     .support-count
       position: absolute
       right: 12px
       bottom: 38px
       height: 18px
       line-height: 18px
       padding: 7px 8px
       background-color: rgba(0,0,0,0.2)
       border-radius: 15px
       .count
         font-size: 10px
         font-weight: 200
         margin-right: 2px
        .icon-keyboard_arrow_right
          font-size: 10px
          vertical-align: middle
     .avatar
        width : 64px
        margin-right : 16px
        float : left
        img
          border-radius : 4px
    .header-background
      position: absolute
      left: 0
      top: 0
      width: 100%
      height: 100%
      filter: blur(5px)
      z-index: -1
    .detail
      position: fixed
      top: 0
      left: 0
      width: 100%
      height: 100%
      overflow: auto
      z-index: 100
      background-color: rgba(7,17,27,0.9)
      backdrop-filter: blur(5px)
      &.fade-enter-active
        transition: all .5s ease
      &.fade-leave-active
        transition: all .5s cubic-bezier(1.0, 0.5, 0.8, 1.0)
      &.fade-enter,&.fade-leave-to
        transform: translateY(20px)
        opacity: 0
      .detail-wrapper
        min-height: 100%
        width: 100%
        .detail-main
          margin-top: 64px
          padding: 0 36px 64px 36px
          .detail-title
            text-align: center
            margin-bottom: 16px
            p
             line-height: 16px
             font-size: 16px
             font-weight: 700
          .detail-star
            text-align: center
          .detail-tips
            display: flex
            margin: 28px 0 24px 0
            .line
              flex: 1
              position: relative
              top: -6px
              border-bottom: 1px solid rgba(255,255,255,0.2)
            .text
              padding: 0 12px
              font-size: 16px
              font-weight: 700
          .yh-out
            padding: 0 12px
            .yh-items
              font-size: 0
              margin-bottom: 12px
              .yh-icon
                display: inline-block
                vertical-align: bottom
                margin-right: 6px
                width: 16px
                height: 16px
                background-size: 16px 16px
                background-repeat: no-repeat
                &.decrease
                  bg-image('./img/decrease_1')
                &.discount
                  bg-image('./img/discount_1')
                &.guarantee
                  bg-image('./img/guarantee_1')
                &.invoice
                  bg-image('./img/invoice_1')
                &.special
                  bg-image('./img/special_1')
              .yh-text
                font-size: 12px
                font-weight: 200
                line-height: 12px
          .bulletin-out
            padding: 0 12px
            p
              font-size: 12px
              line-height: 24px
              font-weight: 200
      .detail-close
        width: 32px
        height: 32px
        margin: -64px auto 0 auto
        clear: both
        .icon-close
          font-size: 32px
  .header
    color : white
    .content-wrapper
     .content
      padding : 2px 0
      float : left
      .title
        margin-bottom : 8px
        .brand
          display : inline-block
          vertical-align : top
          width: 30px
          height: 18px
          bg-image('./img/brand')
          background-size: 30px 18px
          background-repeat: no-repeat
        .name
          margin-left: 6px
          font-size : 16px
          font-weight : bold
          line-height : 18px
      .description
        margin-bottom: 10px
        font-size: 12px
        font-weight: 200
        line-height: 12px
      .support
        font-size: 10px
        font-weight: 200
        line-height: 12px
        .icon
          display: inline-block
          vertical-align: top
          margin-right: 4px
          width: 12px
          height: 12px
          background-size: 12px 12px
          background-repeat: no-repeat
          &.decrease
            bg-image('./img/decrease_1')
          &.discount
            bg-image('./img/discount_1')
          &.guarantee
            bg-image('./img/guarantee_1')
          &.invoice
            bg-image('./img/invoice_1')
          &.special
            bg-image('./img/special_1')
</style>
