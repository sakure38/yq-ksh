<!--
 * @Author: sakure38 2428631045@qq.com
 * @Date: 2022-12-20 20:34:59
 * @LastEditors: sakure38 2428631045@qq.com
 * @LastEditTime: 2022-12-22 19:34:26
 * @FilePath: \practical-training-i-project\src\pages\gudata\gu.vue
 * @Description: 这是默认设置,请设置`customMade`, 打开koroFileHeader查看配置 进行设置: https://github.com/OBKoro1/koro1FileHeader/wiki/%E9%85%8D%E7%BD%AE
-->
<template>
  <div class="echart" id="mychart" style="width:100%; height: 400px;"></div>
</template>

<script>
import * as echarts from "echarts";


export default {
  data() {
    return {};
  },
  mounted() {
    this.initEcharts();
  },
  methods: {
    initEcharts() {
      const option = {
        title: {
          text: "永安药业"
        },
        tooltip: {},
        legend: {},
        xAxis: {
          data: ["2022-12-20", "2022-12-21", "2022-12-22", "2022-12-23"]
        },
        yAxis: {},
        series: [
          {
            type: "candlestick",
            data: [
              [20, 34, 10, 38], //今开、当前价格、最低价格、最高价
              [40, 35, 30, 50],
              [31, 38, 33, 44],
              [38, 15, 5, 42]
            ]
          }
        ]
      };
      const myChart = echarts.init(document.getElementById("mychart"));
      myChart.setOption(option);
      //随着屏幕大小调节图表
      window.addEventListener("resize", () => {
        myChart.resize();
      });
    }
  }
};
</script>

