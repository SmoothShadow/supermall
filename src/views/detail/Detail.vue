<template>
<div id="detail">
<detail-nav-bar @itemClick="titleClick" :current-index="currentIndex"></detail-nav-bar>
  <scroll class="content"
          ref="scroll"
          @scroll="contentScroll"
          :probe-type="3">
    <div>
  <detail-swiper :top-images="topImages" ref="base"></detail-swiper>
  <detail-base-info :goods="goods"></detail-base-info>
  <detail-shop-info :shop="shop"></detail-shop-info>
      <detail-goods-info :detail-info="detailInfo" @imageLoad="imageLoad"></detail-goods-info>
      <detail-param-info :param-info="paramInfo" ref="param"></detail-param-info>
      <detail-comment-info :comment-info="commentInfo" ref="comment"></detail-comment-info>
      <detail-recommend-info :recommend-list="recommendList" ref="recommend"></detail-recommend-info>
    </div>
  </scroll>
  <detail-bottom-bar @addToCart="addToCart"></detail-bottom-bar>
  <back-top @click.native="backTop" class="back-top" v-show="showBackTop">
  </back-top>
</div>
</template>

<script>
import Scroll from "@/components/common/scroll/Scroll";
import DetailNavBar from "@/views/detail/childComps/DetailNavBar";
import {getDetail, getRecommend, Goods, Shop, GoodsParam} from "@/network/detail";
import DetailSwiper from "@/views/detail/childComps/DetailSwiper";
import DetailBaseInfo from "@/views/detail/childComps/DetailBaseInfo";
import DetailShopInfo from "@/views/detail/childComps/DetailShopInfo";
import DetailGoodsInfo from "@/views/detail/childComps/DetailGoodsInfo";
import DetailParamInfo from "@/views/detail/childComps/DetailParamInfo";
import DetailCommentInfo from "@/views/detail/childComps/DetailCommentInfo";
import DetailRecommendInfo from "@/views/detail/childComps/DetailRecommendInfo";
import {BACKTOP_DISTANCE} from "@/common/const";
import DetailBottomBar from "@/views/detail/childComps/DetailBottomBar";
import {debounce} from "@/common/debounce";
import BackTop from "@/components/content/backTop/BackTop";

export default {
name: "Detail",
  data(){
    return {
      iid: null,
      topImages: [],
      goods: {},
      shop: {},
      detailInfo: {},
      paramInfo: {},
      commentInfo: {},
      recommendList: [],
      themeTops: [],
      currentIndex: 0,
      scrollRefresh: null,
      getThemeTopY: null,
      showBackTop: false
    }
  },
  created() {
    // 保存传入的iid
    this.iid = this.$route.params.iid
    console.log(this.$route.params.iid);
    // this.$bus.$on('itemIid', (iid) => {
    //   getDetail(this.iid).then(res => {
    //     console.log(this.iid);
    //     console.log(res)
    //   })
    // })
    getDetail(this.iid).then(res => {
      // console.log(this.iid);
      console.log(res)
      this.topImages = res.data.result.itemInfo.topImages
      console.log(this.topImages)
      const data = res.data.result
      // 获取商品详细信息
      this.goods = new Goods(data.itemInfo, data.columns, data.shopInfo.services)
      console.log(this.goods)
      // 获取店铺信息
      this.shop = new Shop(data.shopInfo);
    //  获取商品信息
      this.detailInfo = data.detailInfo
    //  获取参数信息
      this.paramInfo = new GoodsParam(data.itemParams.info, data.itemParams.rule);
    //  获取评论信息
      if (data.rate.list) {
        // console.log(data.rate.list[0])
        this.commentInfo = data.rate.list[0];
        console.log(this.commentInfo.content)
      }
    })
    // 获取推荐信息
    getRecommend().then((res, error) => {
      if (error) return
      this.recommendList = res.data.data.list
      console.log(this.recommendList)
      // console.log(res);
    })
    this.scrollRefresh = debounce(() => {
      this.$refs.scroll.scroll.refresh()
    }, 100)
    this.getThemeTopY = debounce(() => {
      this.themeTops = []
      this.themeTops.push(this.$refs.base.$el.offsetTop)
      this.themeTops.push(this.$refs.param.$el.offsetTop)
      this.themeTops.push(this.$refs.comment.$el.offsetTop)
      this.themeTops.push(this.$refs.recommend.$el.offsetTop)
      console.log(this.themeTops)
    })
  },
  mounted() {
  this.$bus.$on('imageLoad', () => {
    // this.$refs.scroll.scroll.refresh()
    this.scrollRefresh()
    this.getThemeTopY()
  })
  },
  // updated() {
  //   this.getOffsetTops()
  // },
  methods: {
  imageLoad(){
    // this.$refs.scroll.scroll.refresh()
    this.scrollRefresh()
    this.getThemeTopY()
  },
    backTop(){
      this.$refs.scroll.scroll.scrollTo(0, 0, 300);
    },
    // getOffsetTops() {
    //   this.themeTops = []
    //   this.themeTops.push(this.$refs.base.$el.offsetTop)
    //   this.themeTops.push(this.$refs.param.$el.offsetTop)
    //   this.themeTops.push(this.$refs.comment.$el.offsetTop)
    //   this.themeTops.push(this.$refs.recommend.$el.offsetTop)
    //   this.themeTops.push(Number.MAX_VALUE)
    // },
    contentScroll(position) {
      // 1.监听backTop的显示
      this.showBackTop = position.y < - 1000

      // 2.监听滚动到哪一个主题
      this.listenScrollTheme(-position.y)
    },
    listenScrollTheme(position) {
      let length = this.themeTops.length;
      for (let i = 0; i < length; i++) {
        let iPos = this.themeTops[i];
        if (position >= iPos && position < this.themeTops[i+1]) {
          if (this.currentIndex !== i) {
            this.currentIndex = i;
          }
          break;
        }
      }
    },
    titleClick(index) {
      console.log(this.themeTops[index]);
      console.log(index);
      this.currentIndex = index
      this.$refs.scroll.scroll.scrollTo(0, -this.themeTops[index], 100)
    },
    addToCart() {
      // 1.创建对象
      const obj = {}
      // 2.对象信息
      obj.iid = this.iid;
      obj.imgURL = this.topImages[0]
      obj.title = this.goods.title
      obj.desc = this.goods.desc;
      obj.newPrice = this.goods.nowPrice;
      // 3.添加到Store中
      this.$store.commit('addCart', obj)
    }
  },
  components: {
    DetailNavBar,
    DetailSwiper,
    DetailBaseInfo,
    DetailShopInfo,
    Scroll,
    DetailGoodsInfo,
    DetailParamInfo,
    DetailCommentInfo,
    DetailRecommendInfo,
    DetailBottomBar,
    BackTop
  }
}
</script>

<style scoped>
#detail {
  height: 100vh;
  position: relative;
  z-index: 1;
  background-color: #fff;
}

.content {
  overflow: hidden;
  position: absolute;
  top: 44px;
  bottom: 60px;
}

.back-top {
  position: fixed;
  //z-index: 9;
  right: 10px;
  bottom: 65px;
}
</style>