<template>
  <view class="cart-container" v-if="cart.length !== 0">
    <!-- 收货地址组件 -->
    <my-address></my-address>
    <!-- 购物车商品列表的标题区域 -->
    <view class="cart-title">
      <!-- 左侧的图标 -->
      <uni-icons type="shop" size="18"></uni-icons>
      <!-- 描述文本 -->
      <text class="cart-title-text">购物车</text>
    </view>

    <uni-swipe-action>
      <block v-for="(goods, i) in cart" :key="i">
        <uni-swipe-action-item :right-options="options" @click="swipeItemClickHandler(goods)">
          <!-- 商品列表区域 -->
          <my-goods :goods="goods" :show-radio="true" @radio-change="radioChangeHandler" :show-num="true"
            @num-change="numberChangeHandler"></my-goods>
        </uni-swipe-action-item>
      </block>

    </uni-swipe-action>
    <!-- 使用自定义结算组件 -->
    <my-settle></my-settle>
  </view>
    <!-- 空白购物车区域 -->
    <view class="empty-cart" v-else>
      <image src="/static/cart_empty@2x.png" class="empty-img"></image>
      <text class="tip-text">空空如也~</text>
    </view>
</template>

<script>
  // 按需导入 mapGetters 这个辅助方法
  import {
    mapGetters,
    mapMutations
  } from 'vuex'
  // 导入自己封装的 mixin 模块
  import badgeMix from '@/mixins/tabbar-badge.js'
  // 按需导入 mapState 这个辅助函数
  import {
    mapState
  } from 'vuex'
  export default {
    // 将 badgeMix 混入到当前的页面中进行使用
    mixins: [badgeMix],
    data() {
      return {
        options: [{
          text: '删除',
          style: {
            backgroundColor: '#C00000'
          }
        }]
      }
    },
    // onShow() {
    //   // 在页面刚展示的时候，设置数字徽标
    //   this.setBadge()

    // },
    computed: {

      // 将 m_cart 模块中的 cart 数组映射到当前页面中使用
      ...mapState('m_cart', ['cart']),
    },
    methods: {
      // setBadge() {
      //   // 调用 uni.setTabBarBadge() 方法，为购物车设置右上角的徽标
      //   uni.setTabBarBadge({
      //     index: 2, // 索引
      //     text: this.total + '' // 注意：text 的值必须是字符串，不能是数字
      //   })
      // }
      // 商品的勾选状态发生了变化
      ...mapMutations('m_cart', ['updateGoodsState', 'updateGoodsCount', 'removeGoodsById']),
      radioChangeHandler(e) {
        // console.log(e) // 输出得到的数据 -> {goods_id: 395, goods_state: false}
        this.updateGoodsState(e)
      },
      // 商品的数量发生了变化
      numberChangeHandler(e) {
        this.updateGoodsCount(e)
      },
      swipeItemClickHandler(goods) {
        this.removeGoodsById(goods.goods_id)
      }
    }
  }
</script>

<style lang="scss">
  .cart-title {
    height: 40px;
    display: flex;
    align-items: center;
    font-size: 14px;
    padding-left: 5px;
    border-bottom: 1px solid #efefef;

    .cart-title-text {
      margin-left: 10px;
    }

    .cart-container {
      padding-bottom: 50px;
    }
  }
  .empty-cart {
    display: flex;
    flex-direction: column;
    align-items: center;
    padding-top: 150px;
  
    .empty-img {
      width: 90px;
      height: 90px;
    }
  
    .tip-text {
      font-size: 12px;
      color: gray;
      margin-top: 15px;
    }
  }
</style>
