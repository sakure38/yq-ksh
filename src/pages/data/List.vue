<!--
 * @Author: sakure38 2428631045@qq.com
 * @Date: 2022-12-20 20:27:16
 * @LastEditors: sakure38 2428631045@qq.com
 * @LastEditTime: 2022-12-22 19:22:53
 * @FilePath: \practical-training-i-project\src\pages\data\List.vue
 * @Description: 这是默认设置,请设置`customMade`, 打开koroFileHeader查看配置 进行设置: https://github.com/OBKoro1/koro1FileHeader/wiki/%E9%85%8D%E7%BD%AE
-->
<template>
  <div>
    <div class="topbg">数据列表展示界面</div>
    <!-- 可视化界面雷达图 -->
    <div class="echart" id="mychart"></div>
    <!-- 国内疫情展示区域 -->
    <div class="inLandCovid">
      <!-- 标题 -->
      <div class="realTime">柳州病例</div>
      <!-- 表格数据展示区域 -->
      <div>
        <el-table :data="tableData" style="width: 100%">
          <el-table-column prop="new"> </el-table-column>
          <el-table-column prop="date"> </el-table-column>
          <el-table-column prop="q1"> </el-table-column>
          <el-table-column prop="q2"> </el-table-column>
          <el-table-column prop="q3"> </el-table-column>
          <el-table-column prop="q4"> </el-table-column>
          <el-table-column prop="q5"> </el-table-column>
          <el-table-column prop="q6"> </el-table-column>
          <el-table-column prop="q7"> </el-table-column>
          <el-table-column prop="q8"> </el-table-column>
          <el-table-column prop="q9"> </el-table-column>
          <el-table-column prop="q10"> </el-table-column>
        </el-table>
      </div>
    </div>
    <div class="yyxz">
      <el-dropdown>
  <el-button type="primary">
    不同症状选择<i class="el-icon-arrow-down el-icon--right"></i>
  </el-button>
  <el-dropdown-menu slot="dropdown">
    <el-dropdown-item @click.native="showor2">发烧</el-dropdown-item>
    <el-dropdown-item @click.native="showor3">咳嗽</el-dropdown-item>
    <el-dropdown-item @click.native="showor4">鼻塞</el-dropdown-item>
    <el-dropdown-item @click.native="showor5">关节疼痛</el-dropdown-item>
    <el-dropdown-item @click.native="showor6">眼睛干涩</el-dropdown-item>
  </el-dropdown-menu>
</el-dropdown>
      <div class="echart" id="mychartthirt" v-show="show2"></div>
      <div class="echart" id="mychartfour" v-show="show3"></div>
      <div class="echart" id="mychartfive" v-show="show4"></div>
      <div class="echart" id="mychartsix" v-show="show5"></div>
      <div class="echart" id="mychartseven" v-show="show6"></div>
    </div>
    <div class="yyzz">
      疫情症状展示区域
      <div class="echart" id="mycharttwo"></div>
    </div>
  </div>
</template>

<script>
import * as echarts from "echarts";
// 引入axios
import axios from "axios";
// 引入封装的get请求
import { get } from "@/utils/request";
export default {
  data() {
    return {
      show2: true,
      show3: true,
      show4: true,
      show5: true,
      show6: true,
      isShow: true,
      // 症状数据
      // 发烧
      fszz: {
        ye: {
          q1: 20,
          q2: 30,
          q3: 40
        },
        qn: {
          q1: 24,
          q2: 25,
          q3: 17
        },
        zn: {
          q1: 44,
          q2: 35,
          q3: 40
        },
        ln: {
          q1: 56,
          q2: 44,
          q3: 63
        }
      },
      //咳嗽
      kszz: {
        ye: {
          q1: 30,
          q2: 33,
          q3: 49
        },
        qn: {
          q1: 28,
          q2: 32,
          q3: 35
        },
        zn: {
          q1: 40,
          q2: 43,
          q3: 57
        },
        ln: {
          q1: 60,
          q2: 63,
          q3: 67
        }
      },
      // 鼻塞
      bszz: {
        ye: {
          q1: 25,
          q2: 28,
          q3: 34
        },
        qn: {
          q1: 27,
          q2: 36,
          q3: 25
        },
        zn: {
          q1: 41,
          q2: 32,
          q3: 28
        },
        ln: {
          q1: 54,
          q2: 47,
          q3: 48
        }
      },
      // 关节疼痛
      gjzz: {
        ye: {
          q1: 10,
          q2: 7,
          q3: 11
        },
        qn: {
          q1: 27,
          q2: 37,
          q3: 24
        },
        zn: {
          q1: 40,
          q2: 67,
          q3: 32
        },
        ln: {
          q1: 58,
          q2: 77,
          q3: 62
        }
      },
      // 眼睛干涩
      yjzz: {
        ye: {
          q1: 5,
          q2: 14,
          q3: 23
        },
        qn: {
          q1: 34,
          q2: 27,
          q3: 62
        },
        zn: {
          q1: 41,
          q2: 55,
          q3: 63
        },
        ln: {
          q1: 87,
          q2: 54,
          q3: 66
        }
      },
      
      
      
      

      // 疫情数量相关数据
      desc: {},
      // 国内病例数组
      inLandCovid: [],
      tableData: [
        {
          new: "区域",
          q1: "城中",
          q2: "鱼峰",
          q3: "柳江",
          q4: "柳南",
          q5: "柳北",
          q6: "融水",
          q7: "融安",
          q8: "三江",
          q9: "柳城",
          q10: "鹿寨",
        },
        {
          new: "人数",
          q1: "167",
          q2: "990",
          q3: "20",
          q4: "96",
          q5: "501",
          q6: "380",
          q7: "33",
          q8: "22",
          q9: "36",
          q10: "5",
        },
      ],
    };
  },
  created() {
    // 获取国内病例的方法
    this.getInlandCovidData();
    
  },
  mounted() {
    this.initEcharts();
    this.inittwo();
    this.initthirt();
    this.initfour();
    this.initfive();
    this.initsix();
    this.initseven();
  },
  methods: {
    // 获取疫情数量相关数据

    initEcharts() {
      var option = {
        color: ["#67F9D8", "#FFE434", "#56A3F1", "#FF917C"],
        title: {
          text: "柳州医院疫情数例",
        },
        legend: {},
        radar: [
          {
            indicator: [
              { text: "柳州市人民医院" },
              { text: "柳州市中医院" },
              { text: "柳州人民医院" },
              { text: "柳州市柳铁中心医院" },
              { text: "广西壮族自治区脑科医院" },
            ],
            center: ["25%", "50%"],
            radius: 120,
            startAngle: 90,
            splitNumber: 4,
            shape: "circle",
            axisName: {
              formatter: "【{value}】",
              color: "#428BD4",
            },
            splitArea: {
              areaStyle: {
                color: ["#77EADF", "#26C3BE", "#64AFE9", "#428BD4"],
                shadowColor: "rgba(0, 0, 0, 0.2)",
                shadowBlur: 10,
              },
            },
            axisLine: {
              lineStyle: {
                color: "rgba(211, 253, 250, 0.8)",
              },
            },
            splitLine: {
              lineStyle: {
                color: "rgba(211, 253, 250, 0.8)",
              },
            },
          },
          {
            indicator: [
              { text: "柳州市人民医院", max: 150 },
              { text: "柳州市中医院", max: 150 },
              { text: "柳州市妇幼保健院", max: 150 },
              { text: "柳州市柳铁中心医院", max: 120 },
              { text: "广西壮族自治区脑科医院", max: 108 },
              { text: "柳州人民医院", max: 72 },
            ],
            center: ["75%", "50%"],
            radius: 120,
            axisName: {
              color: "#fff",
              backgroundColor: "#666",
              borderRadius: 3,
              padding: [3, 5],
            },
          },
        ],
        series: [
          {
            type: "radar",
            emphasis: {
              lineStyle: {
                width: 4,
              },
            },

            data: [
              // 人数峰值对象
              {
                value: [100, 8, 0.4, -80, 2000],
                name: "人数峰值",
              },
              {
                value: [60, 5, 0.3, -100, 1500],
                name: "地区周围阳性数据",
                areaStyle: {
                  color: "rgba(255, 228, 52, 0.6)",
                },
              },
            ],
          },
          {
            type: "radar",
            radarIndex: 1,
            data: [
              {
                value: [120, 118, 130, 100, 99, 70],
                name: "阳性",
                symbol: "rect",
                symbolSize: 12,
                lineStyle: {
                  type: "dashed",
                },
                label: {
                  show: true,
                  formatter: function (params) {
                    return params.value;
                  },
                },
              },
              {
                value: [100, 93, 50, 90, 70, 60],
                name: "无症状人数",
                areaStyle: {
                  color: new echarts.graphic.RadialGradient(0.1, 0.6, 1, [
                    {
                      color: "rgba(255, 145, 124, 0.1)",
                      offset: 0,
                    },
                    {
                      color: "rgba(255, 145, 124, 0.9)",
                      offset: 1,
                    },
                  ]),
                },
              },
            ],
          },
        ],
      };
      const myChart = echarts.init(document.getElementById("mychart"));
      myChart.setOption(option);
      //随着屏幕大小调节图表
      window.addEventListener("resize", () => {
        myChart.resize();
      });
    },
    inittwo() {
      var option = {
        tooltip: {
          trigger: "item",
          formatter: "{a} <br/>{b}: {c} ({d}%)",
        },
        legend: {
          data: [
            "咳嗽",
            "鼻塞",
            "发烧",
            "发寒",
            "关节疼",
            "胃痛",
            "头晕",
            "乏力",
            "嗅觉失灵",
            "其他",
          ],
        },
        series: [
          {
            name: "--",
            type: "pie",
            selectedMode: "single",
            radius: [0, "30%"],
            label: {
              position: "inner",
              fontSize: 14,
            },
            labelLine: {
              show: false,
            },
            data: [
              { value: 1548, name: "发烧" },
              { value: 775, name: "咳嗽" },
              { value: 679, name: "鼻塞", selected: true },
            ],
          },
          {
            name: "--",
            type: "pie",
            radius: ["45%", "60%"],
            labelLine: {
              length: 30,
            },
            label: {
              formatter: "{a|{a}}{abg|}\n{hr|}\n  {b|{b}：}{c}  {per|{d}%}  ",
              backgroundColor: "#F6F8FC",
              borderColor: "#8C8D8E",
              borderWidth: 1,
              borderRadius: 4,
              rich: {
                a: {
                  color: "#6E7079",
                  lineHeight: 22,
                  align: "center",
                },
                hr: {
                  borderColor: "#8C8D8E",
                  width: "100%",
                  borderWidth: 1,
                  height: 0,
                },
                b: {
                  color: "#4C5058",
                  fontSize: 14,
                  fontWeight: "bold",
                  lineHeight: 33,
                },
                per: {
                  color: "#fff",
                  backgroundColor: "#4C5058",
                  padding: [3, 4],
                  borderRadius: 4,
                },
              },
            },
            data: [
              { value: 1048, name: "头晕" },
              { value: 335, name: "咳嗽" },
              { value: 310, name: "发寒" },
              { value: 251, name: "乏力" },
              { value: 234, name: "关节疼" },
              { value: 147, name: "嗅觉失灵" },
              { value: 135, name: "胃痛" },
              { value: 102, name: "其他" },
            ],
          },
        ],
      };
      const myChart = echarts.init(document.getElementById("mycharttwo"));
      myChart.setOption(option);
      //随着屏幕大小调节图表
      window.addEventListener("resize", () => {
        myChart.resize();
      });
    },
    initthirt() {
      var option = {
        legend: {},
        tooltip: {},
        dataset: {
          dimensions: ["product", "轻症", "中症", "重症"],
          source: [
            { product: "幼儿", 轻症: this.fszz.ye.q1, 中症: this.fszz.ye.q2, 重症: this.fszz.ye.q3 },
            { product: "青年", 轻症: this.fszz.qn.q1, 中症: this.fszz.qn.q2, 重症: this.fszz.qn.q3 },
            { product: "中年", 轻症: this.fszz.zn.q1, 中症: this.fszz.zn.q2, 重症: this.fszz.zn.q3 },
            { product: "老年", 轻症: this.fszz.ln.q1, 中症: this.fszz.ln.q2, 重症: this.fszz.ln.q3 },
          ],
        },
        xAxis: { type: "category" },
        yAxis: {},
        // Declare several bar series, each will be mapped
        // to a column of dataset.source by default.
        series: [{ type: "bar" }, { type: "bar" }, { type: "bar" }],
      };
      const myChart = echarts.init(document.getElementById("mychartthirt"));
      myChart.setOption(option);
      //随着屏幕大小调节图表
      window.addEventListener("resize", () => {
        myChart.resize();
      });
    },
    initthirt() {
      var option = {
        legend: {},
        tooltip: {},
        dataset: {
          dimensions: ["product", "轻症", "中症", "重症"],
          source: [
            { product: "幼儿", 轻症: this.fszz.ye.q1, 中症: this.fszz.ye.q2, 重症: this.fszz.ye.q3 },
            { product: "青年", 轻症: this.fszz.qn.q1, 中症: this.fszz.qn.q2, 重症: this.fszz.qn.q3 },
            { product: "中年", 轻症: this.fszz.zn.q1, 中症: this.fszz.zn.q2, 重症: this.fszz.zn.q3 },
            { product: "老年", 轻症: this.fszz.ln.q1, 中症: this.fszz.ln.q2, 重症: this.fszz.ln.q3 },
          ],
        },
        xAxis: { type: "category" },
        yAxis: {},
        // Declare several bar series, each will be mapped
        // to a column of dataset.source by default.
        series: [{ type: "bar" }, { type: "bar" }, { type: "bar" }],
      };
      const myChart = echarts.init(document.getElementById("mychartthirt"));
      myChart.setOption(option);
      //随着屏幕大小调节图表
      window.addEventListener("resize", () => {
        myChart.resize();
      });
    },
    initfour() {
      var option = {
        legend: {},
        tooltip: {},
        dataset: {
          dimensions: ["product", "轻症", "中症", "重症"],
          source: [
            { product: "幼儿", 轻症: this.kszz.ye.q1, 中症: this.kszz.ye.q2, 重症: this.kszz.ye.q3 },
            { product: "青年", 轻症: this.kszz.qn.q1, 中症: this.kszz.qn.q2, 重症: this.kszz.qn.q3 },
            { product: "中年", 轻症: this.kszz.zn.q1, 中症: this.kszz.zn.q2, 重症: this.kszz.zn.q3 },
            { product: "老年", 轻症: this.kszz.ln.q1, 中症: this.kszz.ln.q2, 重症: this.kszz.ln.q3 },
          ],
        },
        xAxis: { type: "category" },
        yAxis: {},
        // Declare several bar series, each will be mapped
        // to a column of dataset.source by default.
        series: [{ type: "bar" }, { type: "bar" }, { type: "bar" }],
      };
      const myChart = echarts.init(document.getElementById("mychartfour"));
      myChart.setOption(option);
      //随着屏幕大小调节图表
      window.addEventListener("resize", () => {
        myChart.resize();
      });
    },
    initfive() {
      var option = {
        legend: {},
        tooltip: {},
        dataset: {
          dimensions: ["product", "轻症", "中症", "重症"],
          source: [
            { product: "幼儿", 轻症: this.bszz.ye.q1, 中症: this.bszz.ye.q2, 重症: this.bszz.ye.q3 },
            { product: "青年", 轻症: this.bszz.qn.q1, 中症: this.bszz.qn.q2, 重症: this.bszz.qn.q3 },
            { product: "中年", 轻症: this.bszz.zn.q1, 中症: this.bszz.zn.q2, 重症: this.bszz.zn.q3 },
            { product: "老年", 轻症: this.bszz.ln.q1, 中症: this.bszz.ln.q2, 重症: this.bszz.ln.q3 },
          ],
        },
        xAxis: { type: "category" },
        yAxis: {},
        // Declare several bar series, each will be mapped
        // to a column of dataset.source by default.
        series: [{ type: "bar" }, { type: "bar" }, { type: "bar" }],
      };
      const myChart = echarts.init(document.getElementById("mychartfive"));
      myChart.setOption(option);
      //随着屏幕大小调节图表
      window.addEventListener("resize", () => {
        myChart.resize();
      });
    },
    initsix() {
      var option = {
        legend: {},
        tooltip: {},
        dataset: {
          dimensions: ["product", "轻症", "中症", "重症"],
          source: [
            { product: "幼儿", 轻症: this.gjzz.ye.q1, 中症: this.gjzz.ye.q2, 重症: this.gjzz.ye.q3 },
            { product: "青年", 轻症: this.gjzz.qn.q1, 中症: this.gjzz.qn.q2, 重症: this.gjzz.qn.q3 },
            { product: "中年", 轻症: this.gjzz.zn.q1, 中症: this.gjzz.zn.q2, 重症: this.gjzz.zn.q3 },
            { product: "老年", 轻症: this.gjzz.ln.q1, 中症: this.gjzz.ln.q2, 重症: this.gjzz.ln.q3 },
          ],
        },
        xAxis: { type: "category" },
        yAxis: {},
        // Declare several bar series, each will be mapped
        // to a column of dataset.source by default.
        series: [{ type: "bar" }, { type: "bar" }, { type: "bar" }],
      };
      const myChart = echarts.init(document.getElementById("mychartsix"));
      myChart.setOption(option);
      //随着屏幕大小调节图表
      window.addEventListener("resize", () => {
        myChart.resize();
      });
    },
    initseven() {
      var option = {
        legend: {},
        tooltip: {},
        dataset: {
          dimensions: ["product", "轻症", "中症", "重症"],
          source: [
            { product: "幼儿", 轻症: this.yjzz.ye.q1, 中症: this.yjzz.ye.q2, 重症: this.yjzz.ye.q3 },
            { product: "青年", 轻症: this.yjzz.qn.q1, 中症: this.yjzz.qn.q2, 重症: this.yjzz.qn.q3 },
            { product: "中年", 轻症: this.yjzz.zn.q1, 中症: this.yjzz.zn.q2, 重症: this.yjzz.zn.q3 },
            { product: "老年", 轻症: this.yjzz.ln.q1, 中症: this.yjzz.ln.q2, 重症: this.yjzz.ln.q3 },
          ],
        },
        xAxis: { type: "category" },
        yAxis: {},
        // Declare several bar series, each will be mapped
        // to a column of dataset.source by default.
        series: [{ type: "bar" }, { type: "bar" }, { type: "bar" }],
      };
      const myChart = echarts.init(document.getElementById("mychartseven"));
      myChart.setOption(option);
      //随着屏幕大小调节图表
      window.addEventListener("resize", () => {
        myChart.resize();
      });
    },
    showor2() {
      // 判断张开
      this.show2 = true;
      this.show3 = false;
      this.show4 = false;
      this.show5 = false;
      this.show6 = false;

    },
    showor3() {
      // 判断张开
      this.show2 = false;
      this.show3 = true;
      this.show4 = false;
      this.show5 = false;
      this.show6 = false;

    },
    showor4() {
      // 判断张开
      this.show2 = false;
      this.show3 = false;
      this.show4 = true;
      this.show5 = false;
      this.show6 = false;

    },
    showor5() {
      // 判断张开
      this.show2 = false;
      this.show3 = false;
      this.show4 = false;
      this.show5 = true;
      this.show6 = false;

    },
    showor6() {
      // 判断张开
      this.show2 = false;
      this.show3 = false;
      this.show4 = false;
      this.show5 = false;
      this.show6 = true;

    },
  },
};
</script>

<style scoped>
/* 头部图片 */
.topbg {
  /* 背景图片 */
  background-image: url("/images/4.jpg");
  background-size: 100% 100%;
  line-height: 200px;
  color: white;
  font-size: 30px;
  font-weight: bold;
  text-align: center;
  padding-left: 30px;
  border-radius: 10px;
}
/* 可视化雷达图样式 */
.echart {
  margin-top: 20px;
  width: 100%;
  height: 400px;
}
/* 下拉框样式 */
.block-col-2 {
  margin-top: 20px;
}
.el-dropdown-link {
  cursor: pointer;
  color: #409eff;
}
.el-icon-arrow-down {
  font-size: 12px;
}
.demonstration {
  display: block;
  color: #8492a6;
  font-size: 14px;
  margin-bottom: 20px;
}

/* 疫情症状展示区域 */
.yyzz {
  margin-top: 20px;
  height: 1500px;
}
</style>
