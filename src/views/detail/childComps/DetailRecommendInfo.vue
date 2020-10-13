<template>
  <div class="recommend-info">
    <div class="info-header">热门推荐</div>
    <grid-view>
      <recommend-goods-item v-for="(item, index) in recommendList" :key="index" :goodsItem="item"></recommend-goods-item>
    </grid-view>
  </div>
</template>

<script>
  import GridView from "@/components/common/gridView/GridView";
  import RecommendGoodsItem from "@/components/content/goods/RecommendGoodsItem";

	export default {
		name: "DetailRecommendInfo",
    components: {
		  GridView,
      RecommendGoodsItem
    },
    props: {
		  recommendList: {
		    type: Array
      }
    }
	}
</script>

<style scoped>
  .recommend-info {
    padding: 5px;
  }

  .info-header {
    line-height: 40px;
    padding-left: 8px;
    font-size: 15px;
    color: #333;
  }
</style>
