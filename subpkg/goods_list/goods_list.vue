<template>
  <view>
    <view class="good-list">
      <view v-for="(goods, i) in goodsList" :key="i" @click="gotoDetail(goods)">
        <my-goods :goods=goods></my-goods>
      </view>
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        queryObj: {
          query: '',
          cid: '',
          pagenum: 1,
          pagesize: 10
        },
        goodsList: [],
        total: 0,
        isLoading: false

      };
    },
    onLoad(options) {
      this.queryObj.cid = options.cid || ''
      this.queryObj.query = options.query || ''
      this.getGoodsList()
    },
    methods: {
      // 获取商品列表
      async getGoodsList(cb) {
        //打开节流阀
        this.isLoading = true
        const {
          data: res
        } = await uni.$http.get('https://api-hmugo-web.itheima.net/api/public/v1/goods/search', this
          .queryObj)
          cb && cb()
          this.isLoading = false
        if (res.meta.status !== 200) return uni.$showMsg()
        this.goodsList = [...this.goodsList, ...res.message.goods]
        this.total = res.message.total

      },
      onReachBottom(){
        if(this.queryObj.pagenum * this.queryObj.pagesize >= this.total) return uni.$showMsg('数据加载完毕!')
        if(this.isLoading) return
         // 让页码自增+1
        this.queryObj.pagenum += 1
        this.getGoodsList()
      },
      // 下拉刷新的事件
      onPullDownRefresh() {
        // 1. 重置关键数据
        this.queryObj.pagenum = 1
        this.total = 0
        this.isloading = false
        this.goodsList = []
      
        // 2. 重新发起请求
        this.getGoodsList(() => uni.stopPullDownRefresh())
      },
      gotoDetail(goods){
        //跳转到分包商品详情
        uni.navigateTo({
          url: '/subpkg/goods_detail/goods_detail?goods_id=' + goods.goods_id
        })
      }
    }
  }
</script>

<style lang="scss">

</style>
