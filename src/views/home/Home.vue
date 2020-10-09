<template>
  <div id="home" class="wrapper">
    <nav-bar class="home-nav"><div slot="center">购物街</div></nav-bar>
    <scroll class="content"
            ref="scroll"
            :probe-type="3"
            @scroll="contentScroll"
            :pull-up-load="true"
            @pullingUp="loadMore">
        <home-swiper v-bind:banners="banners"></home-swiper>
        <recommend-view :recommends="recommends"></recommend-view>
        <feature-view></feature-view>
        <tab-control class="tab-control"
                     :titles="['流行', '新款', '精选']"
                     @tabClick="tabClick"></tab-control>
        <goods-list :goods="showGoods"/>
    </scroll>
    <back-top @click.native="backClick" v-show="isShowBackTop"/>
  </div>
<!--<h2>首页</h2>-->
</template>

<script>
import HomeSwiper from "@/views/home/childComps/HomeSwiper";
import RecommendView from "@/views/home/childComps/RecommendView";
import FeatureView from "@/views/home/childComps/FeatureView";

import TabControl from "@/components/content/tabControl/TabControl";
import NavBar from "@/components/common/navbar/NavBar";
import GoodsList from "@/components/content/goods/GoodsList";
import Scroll from "../../../../cloudMusic/src/components/common/Scroll";
import BackTop from "@/components/content/backTop/BackTop";

import {getHomeMultidata, getHomeGoods} from "@/network/home";


export default {
  name: "home",
  components: {
    NavBar,
    HomeSwiper,
    RecommendView,
    FeatureView,
    TabControl,
    GoodsList,
    Scroll,
    BackTop
  },
  data() {
    return {
      banners: [],
      recommends: [],
      goods: {
        'pop': {page: 0, list: []},
        'new': {page: 0, list: []},
        'sell': {page: 0, list: []},
      },
      currentType: 'pop',
      isShowBackTop: false
    }
  },
  computed: {
    showGoods() {
      // console.log(this.goods[this.currentType]);
      return this.goods[this.currentType].list
    }
  },
  created() {
  //  1.请求多个数据
    this.getHomeMultidata()
    //2.请求商品数据
    this.getHomeGoods('pop')
    this.getHomeGoods('new')
    this.getHomeGoods('sell')
  },
  mounted() {
    //  3. 监听item中图片加载完成
    // const refresh = this.debounce(this.$refs.scroll.scroll.refresh, 200)
    this.$bus.$on('itemImageLoad', () => {
      // console.log('图片加载完成')
      // refresh()
      this.$refs.scroll.scroll.refresh()
    })
  },
  methods: {
    // 事件监听方法
    // debounce(func, delay){
    //   let timer =  null
    //   return function (...args){
    //     if (timer) clearTimeout(timer)
    //     timer = setTimeout(() => {
    //       func.apply(this, args)
    //       console.log('---')
    //     }, delay)
    //   }
    // },
    tabClick(index){
      // console.log(index);
      switch(index) {
        case 0:
          this.currentType = 'pop'
          break
        case 1:
          this.currentType = 'new'
          break
        case 2:
          this.currentType = 'sell'
          break
      }
    },
    // 返回顶部按钮
    backClick() {
      // console.log('backClick');
      this.$refs.scroll.scroll.scrollTo(0, 0, 500);
    },
    // 返回顶部按钮的显示和隐藏
    contentScroll(position) {
      // console.log(position);
      this.isShowBackTop = (-position.y) > 1000
    },
    loadMore(){
      this.getHomeGoods(this.currentType);
    },
    // 网络请求方法
    getHomeMultidata() {
      getHomeMultidata().then(res => {
        // console.log(res);
        // this.result = res;
        // 获取首页的轮播图和推荐列表
        this.banners = res.data.data.banner.list;
        this.recommends = res.data.data.recommend.list;
      })
    },
    getHomeGoods(type) {
      // 每次请求都会使page + 1, 请求的是下一页的数据
      const page = this.goods[type].page + 1
      getHomeGoods(type, page).then(res => {
        // console.log(res);
        // 将请求到的数组拼接到goods的对应list里
        this.goods[type].list.push(...res.data.data.list)
        // console.log(this.goods[type].list)
        // 翻页
        this.goods[type].page += 1
        this.$refs.scroll.scroll.finishPullUp()
      })
    }
  }
}
</script>

<style scoped>
#home {
  /*padding-top: 44px;*/
  height: 100vh;
  position: relative;
}

.home-nav {
  background-color: var(--color-tint);
  color: #fff;
  position: fixed;
  left: 0;
  right: 0;
  top: 0;
  z-index: 9;
}

.tab-control {
  position: sticky;
  top: 44px;
  z-index: 9;
}

.content {
  overflow: hidden;

  position: absolute;
  top: 44px;
  bottom: 49px;
  left: 0;
  right: 0;
}

/*.content {*/
/*height: calc(100% - 93px);*/
/*overflow: hidden;*/
/*margin-top: 44px;*/
/*}*/
</style>
