<template>
  <TabBar>
    <TabBarItem path="/home" activeColor="red">
      <img slot="item-icon" src="~assets/img/tabbar/home.png" alt="">
      <img slot="item-icon-active" src="~assets/img/tabbar/home-active.svg" alt="">
      <div slot="item-name">首页</div>
    </TabBarItem>
    <TabBarItem path="/category" activeColor="red">
      <img slot="item-icon" src="~assets/img/tabbar/category.png" alt="">
      <img slot="item-icon-active" src="~assets/img/tabbar/category-active.svg" alt="">
      <div slot="item-name">分类</div>
    </TabBarItem>
    <TabBarItem path="/Cart" activeColor="red">
      <img slot="item-icon" src="~assets/img/tabbar/shopcart.png" alt="">
      <img slot="item-icon-active" src="~assets/img/tabbar/shopcart-active.svg" alt="">
      <div slot="item-name">购物车</div>
    </TabBarItem>
    <TabBarItem path="/profile" activeColor="red">
      <img slot="item-icon" src="~assets/img/tabbar/profile.png" alt="">
      <img slot="item-icon-active" src="~assets/img/tabbar/profile-active.svg" alt="">
      <div slot="item-name">我的</div>
    </TabBarItem>
  </TabBar>
</template>

<script>
import TabBar from "@/components/common/tabbar/TabBar";
import TabBarItem from "@/components/common/tabbar/TabBarItem";
export default {
  name: "MainTabBar",
  components: {
    TabBar,
    TabBarItem
  }
}
</script>

<style scoped>
</style>
