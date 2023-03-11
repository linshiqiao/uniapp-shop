<template>
  <view>
    <view class="search-box">
      <uni-search-bar @input="input" placeholder="请输入搜索内容" :radius="100" cancelButton="none" focus='true'>
      </uni-search-bar>
    </view>
    <!-- 搜索建议列表 -->
    <view class="sugg-list" v-if="searchResults.length !== 0">
      <view class="sugg-item" v-for="(item, i) in searchResults" :key="i" @click="gotoDetail(item)">
        <view class="goods-name">{{item.goods_name}}</view>
        <uni-icons type="forward" size="16"></uni-icons>
      </view>
    </view>
    <!-- 搜索历史 -->
    <view class="history-box" v-else>
      <!-- 标题区域 -->
      <view class="history-title">
        <text>搜索历史</text>
        <uni-icons type="trash" size="24" @click="clean"></uni-icons>
      </view>
      <!-- 列表区域 -->
      <view class="history-list">
        <uni-tag :text="item" v-for="(item, i) in histories" :key="i" @click="gotoGoodsList(item)"></uni-tag>
      </view>
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        // 延时器id
        timer: null,
        // 关键词
        kw: '',
        // 搜索的结果列表
        searchResults: [],
        //搜索历史
        historyList: []
      };
    },
    onLoad(){
        this.historyList = JSON.parse(uni.getStorageSync('kw') || '[]')
    },
    methods: {
      input(e) {
        clearTimeout(this.timer)
        this.timer = setTimeout(() => {
          // console.log(e)
          this.kw = e
          this.getSearchList()
        }, 500)
      },
      async getSearchList() {
        // 判断搜索关键词是否为空
        if (this.kw.length === 0) {
          this.searchResults = []
          return
        }
        const {
          data: res
        } = await uni.$http.get('https://api-hmugo-web.itheima.net/api/public/v1/goods/qsearch', {
          query: this.kw
        })
        // console.log(res)
        if (res.meta.status !== 200) return uni.$showMsg()
        this.searchResults = res.message

        //保存搜索历史记录
        this.saveSearchList()
      },

      gotoDetail(item) {
        uni.navigateTo({
          url: '/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id
        })
      },

      saveSearchList() {
        const index = this.historyList.indexOf(this.kw)
        if (index !== -1) {
          this.historyList.splice(index, 1)
        }
        this.historyList.push(this.kw)
        // 调用 uni.setStorageSync(key, value) 将搜索历史记录持久化存储到本地
        uni.setStorageSync('kw', JSON.stringify(this.historyList))

      },
        
      clean(){
        this.historyList = []
        uni.setStorageSync('kw', '[]')
      },
        
      gotoGoodsList(item){
        uni.navigateTo({
          url:'/subpkg/goods_list/goods_list?query=' + item
        })
      }
    },
    computed: {

      histories() {
        return [...this.historyList].reverse()
      }
    }
  }
</script>

<style lang="scss">
  .search-box {
    position: sticky;
    z-index: 999;
    top: 0;
  }

  .sugg-list {
    padding: 0 5px;

    .sugg-item {
      font-size: 12px;
      padding: 13px 0;
      border-bottom: 1px solid #efefef;
      display: flex;
      align-items: center;
      justify-content: space-between;

      .goods-name {
        // 文字不允许换行（单行文本）
        white-space: nowrap;
        // 溢出部分隐藏
        overflow: hidden;
        // 文本溢出后，使用 ... 代替
        text-overflow: ellipsis;
        margin-right: 3px;
      }
    }
  }

  .history-box {
    padding: 0 5px;

    .history-title {
      display: flex;
      justify-content: space-between;
      align-items: center;
      height: 40px;
      border-bottom: 1px solid #efefef;
    }

    .history-list {
      display: flex;
      flex-wrap: wrap;

      .uni-tag {
        margin: 5px 5px 0 0;
      }
    }
  }
</style>
