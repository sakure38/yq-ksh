<!--
 * @Author: root 2971301532@qq.com
 * @Date: 2022-12-22 07:37:55
 * @LastEditors: root 2971301532@qq.com
 * @LastEditTime: 2022-12-22 19:17:39
 * @FilePath: \practical-training-i-project\src\pages\RealTime.vue
 * @Description: 这是默认设置,请设置`customMade`, 打开koroFileHeader查看配置 进行设置: https://github.com/OBKoro1/koro1FileHeader/wiki/%E9%85%8D%E7%BD%AE
-->
<template>
  <div>
    <div id="container1" style="height: calc(100vh / 2)"></div>
    <div id="container2" style="height: calc(100vh / 2)"></div>
  </div>
</template>

<script>
export default {
  mounted() {
    this.highchartsTwoCity();
    this.highchartsProvince();
  },
  methods: {
    highchartsTwoCity() {
      var chart = Highcharts.chart("container1", {
        chart: {
          type: "line",
        },
        title: {
          text: "近30天柳州和广西壮族自治区感染人数",
        },
        subtitle: {
          text: "数据来源: 模拟数据",
        },
        xAxis: {
          categories: [
            "2022.11.06",
            "2022.11.07",
            "2022.11.08",
            "2022.11.09",
            "2022.11.10",
            "2022.11.11",
            "2022.11.12",
            "2022.11.13",
            "2022.11.14",
            "2022.11.15",
            "2022.11.16",
            "2022.11.17",
            "2022.11.18",
            "2022.11.19",
            "2022.11.20",
            "2022.11.21",
            "2022.11.22",
            "2022.11.23",
            "2022.11.24",
            "2022.11.25",
            "2022.11.26",
            "2022.11.27",
            "2022.11.28",
            "2022.11.29",
            "2022.11.30",
            "2022.12.01",
            "2022.12.02",
            "2022.12.03",
            "2022.12.04",
            "2022.12.05",
          ],
        },
        yAxis: {
          title: {
            text: "感染人数（人）",
          },
        },
        plotOptions: {
          line: {
            dataLabels: {
              // 开启数据标签
              enabled: true,
            },
            // 关闭鼠标跟踪，对应的提示框、点击事件会失效
            enableMouseTracking: true,
          },
        },
        tooltip: {
          headerFormat: '<span style="font-size: 10px">{point.key}</span><br/>',
          pointFormat:
            '<span style="color:{series.color}">\u25CF</span> {series.name}: <b>{point.y}</b><br/>',
          shared: true,
        },
        series: [
          {
            name: "柳州",
            data: [
              17, 46, 87, 90, 34, 27, 16, 2, 0, 0, 1, 0, 0, 3, 0, 0, 0, 0, 0, 0,
              0, 3, 76, 130, 57, 246, 125, 46, 78, 8,
            ],
          },
          {
            name: "广西壮族自治区",
            data: [
              213, 456, 754, 845, 146, 452, 122, 20, 3, 5, 7, 7, 8, 4, 6, 2, 0,
              0, 0, 0, 0, 45, 245, 675, 489, 764, 321, 254, 127, 97,
            ],
          },
        ],
      });
    },
    highchartsProvince() {
      var chart = Highcharts.chart("container2", {
        chart: {
          type: "column",
        },
        title: {
          text: "11月感染人数Top15",
        },
        subtitle: {
          text: "数据截止 2022-12",
        },
        xAxis: {
          type: "category",
          labels: {
            rotation: -45, // 设置轴标签旋转角度
          },
        },
        yAxis: {
          min: 0,
          title: {
            text: "感染人数（人）",
          },
        },
        legend: {
          enabled: false,
        },
        tooltip: {
          pointFormat: "感染人数: <b>{point.y} 人</b>",
        },
        series: [
          {
            name: "总人口",
            data: [
              ["香港", 12347],
              ["台湾", 12034],
              ["北京", 9764],
              ["上海", 8751],
              ["广东", 7645],
              ["江苏", 6452],
              ["浙江", 6234],
              ["福建", 6003],
              ["湖南", 5849],
              ["河北", 5762],
              ["湖北", 4359],
              ["深圳", 3785],
              ["江西", 3645],
              ["山东", 3455],
              ["云南", 3248]
            ],
            dataLabels: {
              enabled: true,
              rotation: -90,
              color: "#FFFFFF",
              align: "right",
              format: "{point.y}", // :.1f 为保留 1 位小数
              y: 10,
            },
          },
        ],
      });
    },
  },
};
</script>

<style scoped>
</style>