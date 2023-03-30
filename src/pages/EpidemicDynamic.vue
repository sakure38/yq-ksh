<template>
  <div class="EpidemicDynamic">
    <!-- 疫情数量展示区域 -->
    <div class="epidemicCount">
      <!-- 当前时间 -->
      <div class="realTime">截止到:{{ new Date() | fmtDate }}</div>
      <!-- 一个图形组件盒子 -->
      <div class="echart" id="mychart" style="width: 100%; height: 600px"></div>
    </div>
    <!-- 国内疫情展示区域 -->
    <div class="inLandCovid">
      <!-- 标题 -->
      <div class="realTime">国内病例</div>
      <!-- 表格数据展示区域 -->
      <el-table :data="inLandCovid" border show-summary style="width: 100%">
        <el-table-column prop="province" label="省份" width="180">
        </el-table-column>
        <el-table-column prop="city" sortable label="城市"> </el-table-column>
        <el-table-column prop="area" sortable label="市区"> </el-table-column>
        <el-table-column prop="confirmed" sortable label="新增确诊">
        </el-table-column>
        <el-table-column prop="suspected" sortable label="新增疑似">
        </el-table-column>
        <el-table-column prop="dead" sortable label="新增死亡">
        </el-table-column>
        <el-table-column prop="cure" sortable label="新增治愈">
        </el-table-column>
        <el-table-column prop="outside" sortable label="境外输入">
        </el-table-column>
      </el-table>
    </div>
    <!-- 标题 -->
    <div class="realTime">最新进展</div>
    <!-- 最新进展展示区域 -->
    <div class="lastedProgress" style="margin-left: -30px">
      <div class="radio">
        <!-- 选择排序方式： -->
        &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
        <el-radio-group v-model="reverse">
          <el-radio :label="true">正序</el-radio>
          <el-radio :label="false">倒序</el-radio>
        </el-radio-group>
      </div>
      <!-- 新闻列表展示区域 -->
      <el-timeline :reverse="reverse">
        <el-timeline-item
          v-for="item in newsData"
          :key="item.id"
          :timestamp="item.publishTime | fmtDate"
          placement="top"
          @click.native="toNewsDetails(item)"
        >
          <el-card>
            <h4>{{ item.title }}</h4>
          </el-card>
        </el-timeline-item>
      </el-timeline>
    </div>
    <!-- 新闻详情的模态框 -->
    <el-dialog
      :title="newsTitle"
      :visible.sync="centerDialogVisible"
      width="70%"
      center
    >
      <!-- 内容展示区域 -->
      <div v-html="newsContent"></div>
      <!-- 底部按钮操作区域 -->
      <span slot="footer" class="dialog-footer">
        <el-button type="primary" @click="centerDialogVisible = false"
          >关 闭</el-button
        >
      </span>
    </el-dialog>
    <template>
      <!-- 返回顶部的小组件 -->
      <el-backtop :bottom="50">
        <div
          style="
             {
              height: 100%;
              width: 100%;
              background-color: #f2f5f6;
              box-shadow: 0 0 6px rgba(0, 0, 0, 0.12);
              text-align: center;
              line-height: 40px;
              color: #1989fa;
            }
          "
        >
          Top
        </div>
      </el-backtop>
    </template>
  </div>
</template>
  </div>
</template>

<script>
// 引入axios
// import axios from "axios";
import * as echarts from "echarts";
// 引入封装的get请求
import { get } from "@/utils/request";
export default {
  data() {
    return {
      //reverse表示是否为正序
      reverse: true,
      // 疫情数量相关数据
      desc: {},
      // 国内病例数组
      inLandCovid: [],
      // 新闻数组
      newsData: [],
      // 控制模态框的显示与隐藏
      centerDialogVisible: false,
      // 新闻的标题
      newsTitle: "",
      // 新闻的内容
      newsContent: "",
    };
  },
  mounted() {
    this.initEcharts();
  },
  created() {
    // 获取国内病例的方法
    this.getInlandCovidData();
    // 获取新闻数据
    this.getNewsData();
  },
  methods: {
    // 获取国内病例相关数据
    async getInlandCovidData() {
      // 设置参数
      let params = {
        // 当前页
        page: 1,
        // 每页展示多少条数据
        pageSize: 10,
      };
      // 发送网络请求
      let res = await get("/epidemic/pageQuery", params);
      console.log(res);
      this.inLandCovid = res.data.list;
    },
    // 获取新闻列表数据
    async getNewsData() {
      let res = await get("/article/findAll");
      //console.log(res);
      this.newsData = res.data;
    },
    // 弹出模态框 显示新闻的详情
    toNewsDetails(item) {
      // 弹出模态框
      this.centerDialogVisible = true;
      // 在模态框里显示新闻的标题
      this.newsTitle = item.title;
      // 在模态框里显示新闻的内容
      this.newsContent = item.content;
    },
    initEcharts() {
      var option = {
        title: {
          text: "疫情信息数据",
          subtext: "左边为每日疫情数据 右边为总疫情数据",
          left: "center",
        },
        tooltip: {
          trigger: "item",
          formatter: "{a} <br/>{b} : {c} ({d}%)",
        },
        legend: {
          left: "center",
          top: "bottom",
        },
        toolbox: {
          show: true,
          feature: {
            mark: { show: true },
            dataView: { show: true, readOnly: false },
            restore: { show: true },
            saveAsImage: { show: true },
          },
        },
        series: [
          {
            name: "今日疫情数据",
            type: "pie",
            radius: [20, 140],
            center: ["25%", "50%"],
            roseType: "radius",
            itemStyle: {
              borderRadius: 5,
            },
            label: {
              show: false,
            },
            emphasis: {
              label: {
                show: true,
              },
            },
            data: [
              { value: 39888, name: "今日确诊" },
              { value: 37211, name: "今日死亡" },
              { value: 41282, name: "今日疑似" },
              { value: 31778, name: "今日痊愈" },
              { value: 36212, name: "今日境外输入" },
            ],
          },
          {
            name: "总共疫情信息",
            type: "pie",
            radius: [20, 140],
            center: ["75%", "50%"],
            roseType: "area",
            itemStyle: {
              borderRadius: 5,
            },
            data: [
              { value: 9731232, name: "总确诊" },
              { value: 8723122, name: "总死亡" },
              { value: 9323323, name: "总疑似" },
              { value: 9662112, name: "总治愈" },
              { value: 7323243, name: "总境外输入" },
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
  },
};
</script>

<style scoped>
/* 设置当前时间和标题的样式 */
.realTime {
  margin: 5px 0;
  color: #999999;
  font-size: 20px;
}
/* 设置第一部分区域的样式 */
.epidemicCount .first {
  display: flex;
  justify-content: space-between;
}
/* 设置每一个盒子的样式 */
.box {
  width: 33%;
  height: 120px;
  background-color: #effaff;
  /* 设置圆角 */
  border-radius: 10px;
  text-align: center;
}
/* 处理盒子里数量的样式 */
.box .count {
  margin-top: 30px;
  font-size: 26px;
  font-weight: bold;
}
/* 处理盒子里标题的样式 */
.box .title {
  margin-top: 5px;
  font-size: 22px;
}
/* 设置第二部分区域的样式 */
.epidemicCount .second {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
}
/* 设置第二部分中盒子的样式 */
.second .box {
  background-color: #fbf5f0;
  margin-bottom: 5px;
}
.lastedProgress {
  width: 90%;
}
</style>
