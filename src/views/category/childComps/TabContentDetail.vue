<template>
  <grid-view>
    <category-goods-item v-for="(item, index) in categoryDetail" :key="index" :goods-item="item"></category-goods-item>
  </grid-view>
</template>

<script>
  import GridView from "@/components/common/gridView/GridView";
  import CategoryGoodsItem from "@/components/content/goods/CategoryGoodsItem";

  export default {
    name: "TabContentDetail",
    components: {
      GridView,
      CategoryGoodsItem
    },
    props: {
      categoryDetail: {
        type: Array,
        default() {
          return []
        }
      }
    }
  }
</script>

<style scoped>

</style>
