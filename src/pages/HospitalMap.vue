<!--
 * @Description:
 * @Author: ZachGmy
 * @Date: 2022-06-28 08:54:35
 * @LastEditors: Please set LastEditors
 * @LastEditTime: 2022-12-20 09:40:06
-->
<template>
  <div>
    <el-button type="primary" @click="back">返回</el-button>
    <div id="container" style="height: 600px"></div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      // 当前医院定位的中心点
      position: [],
    };
  },
  created() {
    // 获取路由跳转传递过来的参数
    // 运用了 解构赋值
    let { longitude, latitude } = this.$route.query;
    this.position = [+longitude, +latitude];
  },
  // 根据center中心点加载地图
  mounted() {
    var map = new AMap.Map("container", {
      zoom: 18, //级别
      center: this.position, //中心点坐标
      viewMode: "3D", //使用3D视图
    });
    // 给地图添加点坐标
    var marker = new AMap.Marker({
      position: this.position, //位置
    });
    map.add(marker); //添加到地图
    // 实时路况的图层
    //实时路况图层
    var trafficLayer = new AMap.TileLayer.Traffic({
      zIndex: 18,
    });
    map.add(trafficLayer); //添加图层到地图
  },
  methods: {
    back() {
      this.$router.go(-1);
    },
  },
};
</script>

<style scoped>
</style>
