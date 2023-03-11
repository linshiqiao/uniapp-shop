<template>
  <view>
    <!-- 轮播图区域 -->
    <swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
      <swiper-item v-for="(item, index) in swiperList" :key="item['goods_id']">
        <navigator class="swiper-item" :url="`/subpkg/goods_detail/goods_detail?goods_id=${item.goods_id}`">
          <image :src="item['image_src']" />
        </navigator>
      </swiper-item>

    </swiper>
    <!-- 分类导航区域 -->
    <view class="nav-list">
      <view class="nav-item" v-for="(item, index) in navList" :key="index" @click="navClickHandler(item)">
        <image :src="item.image_src" class="nav-img"></image>
      </view>
    </view>
    <view class="floor-list">
      <view class="floor-item" v-for="(item, index) in floorList" :key="index">
        <!-- 楼层的标题 -->
        <image :src="item.floor_title.image_src" class="floor-title"></image>
        <!-- 楼层图片区域 -->
        <view class="floor-img-box">
          <!-- 左侧大图片的盒子 -->
          <navigator class="left-img-box" :url="item.product_list[0].url">
            <image :src="item.product_list[0].image_src" :style="{width: item.product_list[0].image_width + 'rpx'}" mode="widthFix"></image>
          </navigator>
          <!-- 右侧小图 片的盒子 -->
          <view class="right-img-box">
          <navigator class="right-img-item" v-for="(item2, index2) in item.product_list" :key="index2" v-if="index2 !== 0" :url="item2.url">
            <image :src="item2.image_src" :style="{width: item2.image_width + 'rpx'}" mode="widthFix"></image>
          </navigator>
          </view>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        //轮播图的数据列表
        swiperList: [],
        //这是分类导航的数据
        navList: [],
        //楼层的数据
        floorList: []
      };
    },
    onLoad() {
      this.getSwiperList()
      this.getNavList()
      this.getFloorList()
    },
    methods: {
      async getSwiperList() {
        const result = await uni.$http.get('https://api-hmugo-web.itheima.net/api/public/v1/home/swiperdata')
        // console.log(result)
        if (result.statusCode == 200) {
          this.swiperList = result.data.message
          // uni.$showMsg('数据请求成功!')
        } else {
          return uni.$showMsg()
        }
      },
      async getNavList() {
        const result = await uni.$http.get('https://api-hmugo-web.itheima.net/api/public/v1/home/catitems')
        // console.log(result)
        if (result.statusCode == 200) {
          this.navList = result.data.message
          // uni.$showMsg('数据请求成功!')
        } else {
          return uni.$showMsg()
        }
      },
      navClickHandler(item) {
        if (item.name === '分类') {
          uni.switchTab({
            url: '/pages/cate/cate'
          })
        }
      },

      async getFloorList() {
        const result = await uni.$http.get('https://api-hmugo-web.itheima.net/api/public/v1/home/floordata')
        // console.log(result)
        if (result.statusCode == 200) {
          let list = result.data.message
          list.forEach(floor => {
            floor.product_list.forEach(prod => {
              prod.url = '/subpkg/goods_list/goods_list?' + prod.navigator_url.split('?')[1]
            })
          })
          this.floorList = list
          // uni.$showMsg('数据请求成功!')
        } else {
          return uni.$showMsg()
        }
      }
    }
  }
</script>

<style lang="scss">
  swiper {
    height: 330rpx;

    .swiper-item,
    image {
      width: 100%;
      height: 100%;
    }
  }

  .nav-list {
    display: flex;
    justify-content: space-around;
    margin: 15rpx 0;

    .nav-img {
      width: 128rpx;
      height: 140rpx;
    }
  }

  .floor-title {
    height: 60rpx;
    width: 100%;
  }
  .right-img-box{
    display: flex;
    flex-wrap: wrap;
    justify-content: space-around;
  }
  .floor-img-box{
    display: flex;
    padding: 10rpx;
  }
</style>
