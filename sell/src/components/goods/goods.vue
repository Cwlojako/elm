<template>
  <div class="goods">
    <div class="menu-wrapper" ref="menuWrapper">
      <ul class="menu-ul">
        <li class="menu-item" v-for="(item,index) in goods" :key="index" 
        :class="{current:currentIndex === index}" @click="selectMenu(index,$event)">
          <span class="text border-1px">
            <span class="icon" v-show="item.type>0" :class="classMap[item.type]"></span>{{item.name}}
          </span>
        </li>
      </ul>
    </div>
    <div class="foods-wrapper" ref="foodWrapper">
      <ul>
        <li v-for="(item,index) in goods" class="food-list food-list-hook" :key="index">
          <h1 class="title">{{item.name}}</h1>
          <ul>
            <li v-for="(food,index) in item.foods" class="food-item border-1px" :key="index">
              <div class="icon">
                <img :src="food.icon" width="80px" height="80px">
              </div>
              <div class="content">
                <h2 class="name">{{food.name}}</h2>
                <p class="desc" v-show="food.description">{{food.description}}</p>
                <div class="extra">
                  <span class="extra-mounth">月售{{food.sellCount}}份</span>
                  <span class="extra-good">好评率<span class="rating">{{food.rating}}%</span></span>
                </div>
                <div class="price">
                  <span class="currentPrice"><span class="yuan">￥</span>{{food.price}}</span>
                  <s class="oldPrice" v-show="food.oldPrice">￥{{food.oldPrice}}</s>
                </div>
                <div class="cartcontrol-wrapper">
                  <!--在父组件监听到子组件触发的cart-add事件-->
                  <cartcontrol :food="food" @cart-add="handlecartAdd"></cartcontrol>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <shopcar ref="shopcar" :selectFoods="selectFoods" :deliveryPrice="seller.deliveryPrice" :minPrice="seller.minPrice"></shopcar>
  </div>
</template>
<script type="text/ecmascript-6">
  import BScroll from 'better-scroll'
  import shopcar from '@/components/shopcar/shopcar.vue'
  import cartcontrol from '@/components/cartcontrol/cartcontrol.vue'

  const ERR_OK = 0
  export default {
    props: {
      seller: {
        type: Object
      }
    },
    data() {
      return {
        goods: [],
        listHeight: [],
        scrollY: 0
      }
    },
    computed: {
      currentIndex() {
        for (let i = 0; i < this.listHeight.length; i++) {
          let height1 = this.listHeight[i]
          let height2 = this.listHeight[i + 1]
          if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
            return i
          }
        }
        return 0
      },
      selectFoods() {
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
    created() {
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']

      this.$http.get('/api/goods').then((response) => {
        response = response.body
        if (response.errno === ERR_OK) {
          this.goods = response.data
          this.$nextTick(() => {
            this.initScroll()
            this.caculateHeight()
          })
          console.log(this.goods)
        }
      })
    },
    methods: {
      selectMenu(index, event) {
        if (!event._constructed) {
          return
        }
        let foodList = this.$refs.foodWrapper.getElementsByClassName('food-list-hook')
        let el = foodList[index]
        this.foodScroll.scrollToElement(el, 300)
      },
      _drop(target) { // 在goods.vue定义_drop方法将cartcontrol传递过来的target对象再传递给shopcar
        // 使用nextTick优化动画体验
        this.$nextTick(() => {
          // 通过$ref属性访问shopcar子组件的drop方法
          this.$refs.shopcar.drop(target)
        })
      },
      // 点击加号按钮触发事件
      handlecartAdd(target) {
        this._drop(target) // 调用_drop方法
      },
      initScroll() {
        this.menuScroll = new BScroll(this.$refs.menuWrapper, {
          click: true
        })
        this.foodScroll = new BScroll(this.$refs.foodWrapper, {
          probeType: 3,
          click: true
        })
        this.foodScroll.on('scroll', (pos) => {
          this.scrollY = Math.abs(Math.round(pos.y))
        })
      },
      caculateHeight() {
        let foodList = this.$refs.foodWrapper.getElementsByClassName('food-list-hook')
        let height = 0
        this.listHeight.push(height)
        for (let i = 0; i < foodList.length; i++) {
          height += foodList[i].clientHeight
          this.listHeight.push(height)
        }
      }
    },
    components: {
      shopcar,
      cartcontrol
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import '../../common/stylus/mixin.styl'
  .goods
    display:flex
    position: absolute
    top: 171px
    bottom: 46px
    width: 100%
    overflow: hidden
    .menu-wrapper
      flex: 0 0 80px
      background-color: #f3f5f7
      .menu-ul
        .menu-item
          display: table
          height: 54px
          width: 56px
          line-height: 14px
          padding:0 12px
          &:last-child
            .text
              border-1px-none()
          &.current
            position: relative
            top: -1px
            background-color: rgb(255,255,255)
            .text
              font-weight: 700
              border-1px-none()
          .icon
            display: inline-block
            vertical-align: top
            margin-right: 2px
            width: 12px
            height: 12px
            background-size: 12px 12px
            background-repeat: no-repeat
            &.decrease
              bg-image('./img/decrease_3')
            &.discount
              bg-image('./img/discount_3')
            &.guarantee
              bg-image('./img/guarantee_3')
            &.invoice
              bg-image('./img/invoice_3')
            &.special
              bg-image('./img/special_3')
          .text
            display: table-cell
            width: 56px
            vertical-align: middle
            font-size: 12px
            font-weight: 200
            border-1px(rgba(7,17,27,0.1))
    .foods-wrapper
      flex: 1
      .food-list
        .title
          height: 26px
          line-height: 26px
          padding-left: 14px
          font-weight: 900
          font-size: 12px
          background-color: #f3f5f7
          color: rgb(147,153,159)
          border-left: 2px solid #d9dde1
        .food-item
          display: flex
          margin: 18px
          padding-bottom: 18px
          border-1px(rgba(7,17,27,0.1))
          &:last-child
            border-1px-none()
            margin-bottom: 0
          .icon
            flex: 0 0 80px
            img 
             border-radius: 5px
          .content
            flex: 1
            padding: 2px 0 0 10px
            .name
              margin-bottom: 8px
              font-size: 14px
              font-weight: 900
              line-height: 14px
              color: rgb(7,17,27)
            .desc
              margin-bottom: 8px
              font-size: 10px
              font-weight: 200
              line-height: 10px
              color: rgb(147,153,149)
              width: 50px
              overflow: hidden
              text-overflow:ellipsis
              white-space: nowrap
            .extra
              margin-bottom: 8px
              font-size:0
              font-weight: 200
              line-height: 10px
              color: rgb(147,153,149)
              .extra-mounth
                margin-right: 8px
                font-size: 10px
              .extra-good
                font-size: 10px
                .rating
                  color: red
                  font-weight: 700
            .price
              color: red
              .currentPrice
                margin-right: 2px
                font-size: 16px
                font-weight: 700
                .yuan
                  font-size: 10px
                  font-weight: normal
              .oldPrice
                font-size: 10px
                font-weight: normal
                color: rgb(147,153,159)
            .cartcontrol-wrapper
              position: absolute
              right: 0
              bottom: 12px
</style>
