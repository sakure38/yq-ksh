<!--
 * @Description:
 * @Author: ZachGmy
 * @Date: 2022-06-29 18:44:41
 * @LastEditors: 'JIU' 3157123393@qq.com
 * @LastEditTime: 2022-12-21 17:09:05
-->
<template>
  <div>
    <div id="liuzhou_container" style="height: 700px"></div>
  </div>
</template>

<script>
// 引入axios
import axios from "axios";
import liuzhou from "../assets/liuzhou.json"
export default {
  data() {
    return {      
      // 控制加载动画是否显示
      loading: true,
    };
  },
  mounted() {
    // 获取国内疫情数据
    // 因为接口限制原因 暂时采用本地数据进行处理
    // this.getChinaCovidData();
    // 调用本地数据 加载地图
    this.loadMap()
  },
  methods: {
    // 加载地图
    loadMap() {
      var data = [
        { name: "城中", value: 167 },
        { name: "鱼峰", value: 990 },
        { name: "柳江", value: 20 },
        { name: "柳南", value: 96 },
        { name: "柳北", value: 501 },
        { name: "融水", value: 380 },
        { name: "融安", value: 33 },
        { name: "三江", value: 22 },
        { name: "柳城", value: 36 },
        { name: "鹿寨", value: 5 },
      ];
      // 初始化图表
      new Highcharts.Map("liuzhou_container", {
        // 主标题
        title: {
          text: "柳州市疫情现有确诊分布",
        },
        colorAxis: {
          // 设置每个区间的颜色
          dataClasses: [
            {
              color: "#FFE1B3",
              to: 10,
            },
            {
              // 颜色值
              color: "#FDCFA7",
              // 从哪开始
              from: 10,
              // 到哪结束
              to: 50,
            },
            {
              color: "#FBB998",
              from: 50,
              to: 200,
            },
            {
              color: "#FAA68A",
              from: 200,
              to: 500,
            },
            {
              color: "#F8947D",
              from: 500,
              to: 1000,
            },
            {
              color: "#F57669",
              from: 1000,
              to: 5000,
            },
            {
              color: "#F2544E",
              from: 5000,
            },
          ],
        },
        // 添加点击事件
        plotOptions: {
          series: {
            events: {
              // 地图的点击方法
              click(event) {
                console.log(event);
              },
            },
          },
        },
        series: [
          {
            // 数据源
            data: data,
            name: "现存确诊",
            mapData: liuzhou,
            joinBy: "name", // 根据 name 属性进行关联
          },
        ],
      });
    },  },
};
</script>

<style scoped>
</style>