<template>
    <div class="shopcar">
        <div class="content" @click="toggleList">
            <div class="content-left">
                <div class="logo-wrapper">
                    <div class="logo" :class="{highLight: totalCount>0}">
                        <i class="icon-shopping_cart"></i>
                        <div v-show="totalCount>0" class="count">{{totalCount}}</div>
                    </div>
                </div>
                <div class="price" :class="{highLight: totalPrice>0}"><span>￥{{totalPrice}}</span></div>
                <div class="desc"><span>配送费￥{{deliveryPrice}}</span></div>
            </div>
            <div class="content-right" :class="{highLight: payDesc === '去结算'}">
                <div class="text">{{payDesc}}</div>
            </div>
        </div>
        <div class="ball-container">
            <div v-for="(ball,index) in balls" :key="index">
                <transition @before-enter="handleBeforeEnter"
                            @enter="handleEnter"
                            @after-enter="handleAfterEnter"
                            name="drop">
                    <div class="ball" v-show="ball.show"> <!--外层盒子-->
                        <div class="inner inner-hook"></div> <!--内层盒子-->
                    </div> 
                </transition>
            </div>
        </div>
        <transition name="up">
            <div class="shopcart-list" v-show="listShow">
                <div class="list-header">
                    <h1 class="title">购物车</h1>
                    <span class="empty" @click="emptyCart">清空</span>
                </div>
                <div class="list-content" ref="listContent">
                    <ul>
                        <li class="food" v-for="(food,index) in selectFoods" :key="index">
                            <span class="name">{{food.name}}</span>
                            <div class="price">
                                <span class="yuan">￥</span><span class="total">{{food.price*food.count}}</span>  
                            </div>
                            <div class="cartcontrol-wrapper">
                                <cartcontrol :food="food" @cart-add="handlecartAdd"></cartcontrol>
                            </div>
                        </li>
                    </ul>
                </div>
            </div>
        </transition>
        <transition name="fade">
            <!-- listShow表示当list详情列表显示的时候mask才显示 -->
            <div class="list-mask" v-show="listShow" @click="hideList()"></div>
        </transition>
    </div>
</template>

<script type='text/ecmascript-6'>
    import BScroll from 'better-scroll'
    import cartcontrol from '@/components/cartcontrol/cartcontrol.vue'
    export default {
       props: {
            selectFoods: {
                type: Array,
                default() {
                    return []
                }
            },
            deliveryPrice: {
                type: Number,
                default: 0
            },
            minPrice: {
                type: Number,
                default: 0
            }
        },
        data() {
            return {
                // 使用balls存放5个小球，这些小球的默认状态都是不显示
                balls: [{show: false}, {show: false}, {show: false}, {show: false}, {show: false}],
                dropBalls: [], // 存放掉落的小球
                fold: true
            }
        },
        methods: {
            handlecartAdd(target) {
                this.drop(target)
            },
            // 当触发drop方法时小球开始掉落
            drop(el) {
                for (let i = 0; i < this.balls.length; i++) {
                    let ball = this.balls[i]
                    if (!ball.show) { // 当小球显示状态为隐藏时
                        ball.show = true
                        ball.el = el // 将cartControl传过来的对象挂载到ball的el属性上
                        this.dropBalls.push(ball)
                        return
                    }
                }
            },
            toggleList() {
                if (!this.totalCount) {
                    return
                }
                this.fold = !this.fold
            },
            // beforeEnter
            handleBeforeEnter: function(el) {
                let count = this.balls.length
                while (count--) {
                    let ball = this.balls[count]
                    if (ball.show) {
                        //  getBoundingClientRect()获取小球相对于视窗的位置，屏幕左上角坐标为0，0
                        let rect = ball.el.getBoundingClientRect() 
                        // 小球x方向位移= 小球距离屏幕左侧的距离-外层盒子距离水平的距离
                        let x = rect.left - 32
                        // 负数，因为是从左上角向下
                        let y = -(window.innerHeight - rect.top - 22)
                        el.style.display = ''
                        el.style.webkitTransform = `translate3d(0,${y}px,0)`
                        el.style.transform = `translate3d(0,${y}px,0)`
                        let inner = el.getElementsByClassName('inner-hook')[0]
                        // 设置内层盒子，即小球水平方向的距离
                        inner.style.webkitTransform = `translate3d(${x}px,0,0)` 
                        inner.style.transform = `translate3d(${x}px,0,0)`
                    }
                }
            },
            // enter
            handleEnter: function(el, done) {
                /* eslint-disable no-unused-vars */
                // 触发浏览器重绘
                let rf = el.offsetHeight
                this.$nextTick(() => {
                    el.style.webkitTransform = 'translate3d(0, 0, 0)'
                    el.style.transform = 'translate3d(0, 0, 0)'
                    let inner = el.getElementsByClassName('inner-hook')[0]
                    inner.style.webkitTransform = 'translate3d(0, 0, 0)'
                    inner.style.transform = 'translate3d(0, 0, 0)'
                    // Vue为了知道过渡的完成，必须设置相应的事件监听器。它可以是transitionend或 animationend
                    el.addEventListener('transitionend', done) 
                })
            },
            // AfterEnter
            handleAfterEnter: function(el) {
                // 完成一次动画就删除一个dropBalls的小球
                let ball = this.dropBalls.shift()
                if (ball) {
                    ball.show = false
                    el.style.display = 'none'
                }
            },
            // 因为在computed里面无法对data里的数据进行修改或者重新赋值，所以可以调出来用方法实现修改
            foldTure() {
                this.fold = true
            },
             // 因为在computed里面无法对data里的数据进行修改或者重新赋值，所以可以调出来用方法实现修改
            shopCartScroll() {
                this.$nextTick(() => {
                    if (!this.scroll) { // 没有scroll的时候才需要new，否则只需要刷新一下即可
                        this.scroll = new BScroll(this.$refs.listContent, {
                            click: true
                        })
                    } else {
                        this.scroll.refresh()
                    }
                })
            },
            // 清空购物车
            emptyCart() {
                this.selectFoods.forEach((food) => {
                    food.count = 0
                })
            },
            // 点击mark层，购物车详情列表被收回
            hideList() { 
                this.fold = true
            }
        },
        computed: {
            // 计算总价格
            totalPrice() {
                let total = 0
                this.selectFoods.forEach((food) => {
                    total += food.price * food.count
                })
                return total
            },
            // 计算总数量
            totalCount() {
                let count = 0
                this.selectFoods.forEach((food) => {
                    count += food.count
                })
                return count
            },
            // 起送差价
            payDesc() {
                if (this.totalPrice === 0) {
                    return `￥${this.minPrice} 起送`
                } else if (this.totalPrice < this.minPrice) {
                    let diff = this.minPrice - this.totalPrice
                    return `还差￥${diff} 起送`
                } else {
                    return `去结算`
                }
            },
            listShow() {
                if (!this.totalCount) {
                    this.foldTure()
                    return false
                }
                let show = !this.fold
                if (show) { // true为展示状态才实现购物车详情页的滚动
                    this.shopCartScroll()
                }
                return show
            }     
        },
        components: {
            cartcontrol
        }
    }
</script>

<style lang='stylus' rel='stylesheet/stylus'>
    @import '../../common/stylus/mixin.styl'
    .shopcar
      position: fixed
      left: 0
      bottom: 0
      z-index: 99
      width: 100%
      height: 48px
      .content
        display: flex
        background-color: #141d27
        .content-left
          flex: 1
          font-size: 0
          .logo-wrapper
            display: inline-block
            position: relative 
            top: -10px
            margin: 0 12px
            padding: 6px
            width: 56px
            height: 56px
            box-sizing: border-box
            vertical-align: top
            border-radius: 50%
            background-color: #141d27
            .logo
             position: relative
             width: 100%
             height: 100%
             border-radius: 50%
             text-align: center
             background-color: #2b343c
             &.highLight
               background-color: rgb(0,160,220)
               .icon-shopping_cart
                 color: #fff
             .icon-shopping_cart
               font-size: 24px
               color: #80858a
               line-height: 44px
             .count
               position: absolute
               top: -5px
               right: -8px
               width: 24px
               height: 16px
               line-height: 16px
               text-align: center
               font-size: 10px
               font-weight: 700
               border-radius: 6px
               color: #fff
               background-color: rgb(240,20,20)
               box-shadow: 0 4px 8px 0 rgba(0,0,0,0.4)
          .price
            display: inline-block
            vertical-align: top
            font-size: 16px
            color: rgba(255,255,255,0.4)
            line-height: 28px
            padding: 10px 0
            &.highLight
              color: #fff
              font-weight: 900
            span
              padding-right: 8px
              border-right: 1px solid rgba(255,255,255,0.1)
          .desc
            display: inline-block
            vertical-align: top
            font-size: 12px
            color: rgba(255,255,255,0.4)
            line-height: 28px
            padding: 10px 8px
        .content-right
          flex: 0 0 105px
          width: 105px
          padding: 0 8px
          box-sizing: border-box
          background-color: #2b343c
          text-align: center
          &.highLight
            background-color: #00b43c
            .text
              color: #fff
              font-weight: 900
          .text
            font-size: 12px
            font-weight: 700
            color: rgba(255,255,255,0.4)
            height: 48px
            line-height: 48px
      .ball-container
        .ball
          position: fixed 
          left: 32px
          bottom: 22px
          z-index: 999
          &.drop-enter-active, &.drop-leave-active
            transition: all .8s cubic-bezier(0.49,-0.49,0.75,0.41)
            .inner
              width: 16px
              height: 16px
              border-radius: 50%
              background: rgb(0,160,220)
              transition: all .8s
      .shopcart-list
          position: absolute 
          left: 0
          bottom: 48px
          z-index: -1
          width: 100%
          &.up-enter-active
            transition: all 1s
          &.up-leave-active
            transition: all 1s
          &.up-enter,&.up-leave-to
            transform: translateY(100%) 
          .list-header
              height: 40px
              line-height: 40px
              background: #f3f5f7
              padding: 0 18px
              border-1px(rgba(7,17,27,0.1))
              .title
                display: inline-block
                font-size: 14px
                font-weight: 400
                line-height: 40px
                color: rgb(0,0,0)
              .empty
                float: right
                font-size: 12px
                font-weight: 700
                line-height: 40px
                color: rgb(0,160,220)
          .list-content
              background: #fff
              padding: 0 18px
              max-height: 217px
              overflow: hidden
              .food
                height: 48px
                line-height: 48px
                border-1px(rgba(7,17,27,0.1))
                .name
                  display: inline-block
                  font-size: 14px
                  color: rgb(7,17,27)
                  font-weight: 700
                  line-height: 48px
                  padding-left: 18px
                .price
                  position: absolute 
                  right: 90px
                  bottom: 12px      
                  color: rgb(240,20,20)
                  line-height: 24px
                  .yuan
                    font-size: 10px
                    font-weight: normal
                  .total
                    font-size: 14px
                    font-weight: 700
                .cartcontrol-wrapper
                  position: absolute
                  right: 0
                  bottom: 6px
                  .cartcontrol
                    height: 36px
      .list-mask
        position: fixed 
        top: 0
        left 0
        width: 100%
        height: 100%
        z-index: -2 //z-index要小于shopcart详情页(-1)的z-index
        backdrop-filter: blur(10px) // 模糊效果
        -webkit-backdrop-filter: blur(10px)
        background: rgba(7, 17, 27, 0.8)
        &.fade-enter-active, &.fade-leave-active  
            transition: all 0.5s //设置缓动效果
        &.fade-enter, &.fade-leave-to 
            opacity: 0
</style>
