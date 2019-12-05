<template>
    <div class="cartcontrol">
        <transition name="fade">
            <div class="cart-decrease icon-remove_circle_outline" @click="decreaseCart" v-show="food.count>0"></div>
        </transition>
        <div class="cart-count" v-show="food.count>0">{{food.count}}</div>
        <div class="cart-add icon-add_circle" @click="addCart"></div>
    </div>
</template>

<script type='text/ecmascript-6'>
    import Vue from 'vue'
    export default {
        props: {
            food: {
                type: Object
            }
        },
        methods: {
            addCart(event) {
                // 检测事件派发是否来自于better-scroll，
                // 不是则return掉，避免PC端点击的时候触发两次点击事件
                if (!event._constructed) {
                    return
                }
                // 当给一个观测对象添加一个它不存在的属性时，直接赋值是不可以的
                // 需要使用Vue.set设置这个属性
                if (!this.food.count) {
                    Vue.set(this.food, 'count', 1)
                } else {
                    this.food.count++
                }
                // 向父组件触发一个自定义的cart-add事件，并将事件对象传递给父组件
                this.$emit('cart-add', event.target)
            },
            decreaseCart(event) {
                if (!event._constructed) {
                    return
                }
                if (this.food.count) {
                    this.food.count--
                }
            }
        }
    }
</script>

<style lang='stylus' rel='stylesheet/stylus'>
    .cartcontrol
       font-size: 0
       .cart-decrease, .cart-add
          display: inline-block
          padding: 6px
          line-height: 24px
          font-size: 24px
          color: rgb(0,160,220)
        .cart-count
          display: inline-block
          vertical-align: top
          width: 8px
          font-size: 10px
          line-height: 36px
          text-align: center
          color: rgb(147,153,159)
        .cart-decrease
          color: rgb(0,160,220)
          &.fade-enter-active
            transition: all .3s ease
          &.fade-leave-active
            transition: all .3s cubic-bezier(1.0, 0.4, 0.8, 1.0)
          &.fade-enter,&.fade-leave-to
            transform: translateX(20px) rotate(90deg)
            opacity: 0
</style>
