<!--
 *                        _oo0oo_
 *                       o8888888o
 *                       88" . "88
 *                       (| -_- |)
 *                       0\  =  /0
 *                     ___/`---'\___
 *                   .' \\|     |// '.
 *                  / \\|||  :  |||// \
 *                 / _||||| -:- |||||- \
 *                |   | \\\  - /// |   |
 *                | \_|  ''\---/''  |_/ |
 *                \  .-\__  '-'  ___/-. /
 *              ___'. .'  /--.--\  `. .'___
 *           ."" '<  `.___\_<|>_/___.' >' "".
 *          | | :  `- \`.;`\ _ /`;.`/ - ` : | |
 *          \  \ `_.   \_ __\ /__ _/   .-` /  /
 *      =====`-.____`.___ \_____/___.-`___.-'=====
 *                        `=---='
 * 
 * 
 *      ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
 * 
 *            佛祖保佑     永不宕机     永无BUG
 * 
 * 创建日期:
 * @Date: 2022-12-20 16:53:44
 * 文件路径:
 * @FilePath: \web课设d:\实训1\码云\src\pages\data\Input.vue
 -->
<template>
  <div>
    <!-- 背景 -->
    <div class="back">数据上报管理模块</div>

    <!-- 走马灯 -->
    <div class="block">
      <div>
        <el-carousel height="110px" trigger="click">
          <!-- 循环数组产生走马灯 -->
          <el-carousel-item v-for="item in news" :key="item.id">
            <h3 class="small">
              <div class="light_title">
                <div>
                  <span class="demonstration">新闻轮播:</span>
                </div>
                <br />
                <div>{{ item.title }}</div>
              </div>
            </h3>
          </el-carousel-item>
        </el-carousel>
      </div>
    </div>
    <br /><br />

    <!-- 下拉菜单和提交按钮 -->
    <div class="select">
      <div style="font-size: 24px; line-height: 40px; font-weight: bolder">
        请选择你要上报信息的所属模块:&nbsp;&nbsp;
      </div>
      <div class="menu">
        <el-dropdown @command="handleCommand">
          <el-button type="primary">
            {{ choose }}<i class="el-icon-arrow-down el-icon--right"></i>
          </el-button>
          <el-dropdown-menu slot="dropdown">
            <el-dropdown-item command="疫情数据模块"
              >疫情数据模块</el-dropdown-item
            >
            <el-dropdown-item command="医院数据模块"
              >医院数据模块</el-dropdown-item
            >
            <el-dropdown-item command="新闻数据模块"
              >新闻数据模块</el-dropdown-item
            >
          </el-dropdown-menu>
        </el-dropdown>
      </div>
      <div class="bto">
        <el-row>
          <el-button type="danger" round @click="dialog(choose)"
            >数据上报</el-button
          >
        </el-row>
      </div>
    </div>
    <br /><br />

    <!-- 疫情数据模块 -->
    <div>
      <div class="right">
        <el-button @click="show3 = !show3">点击隐藏或展开疫情数据</el-button>
      </div>
      <div style="margin-top: 20px">
        <el-collapse-transition>
          <div v-show="show3">
            <div class="now_data">
              <div class="realTime">
                截止到{{ new Date() | fmtDate }}的疫情数据柱状图:
              </div>
              <div
                class="echart"
                id="mychart"
                style="width: 100%; height: 400px"
              ></div>
            </div>
          </div>
        </el-collapse-transition>
      </div>
    </div>
    <br /><br />

    <!-- 医院数据模块 -->
    <div class="hos_data">
      <div class="right">
        <el-button @click="show1 = !show1">点击隐藏或展开医院数据</el-button>
      </div>
      <div style="margin-top: 20px">
        <el-collapse-transition>
          <div v-show="show1">
            <el-table :data="hospital_data" style="width: 100%">
              <el-table-column prop="name" label="医院名称"> </el-table-column>
              <el-table-column prop="introduce" label="医院类型">
              </el-table-column>
              <el-table-column label="医院地址">
                <!-- 非常重要的作用域插槽，干掉prop属性，自己拼接多个属性来当成一个使用，row保存的是整个数组对象 -->
                <template slot-scope="scope">
                  {{
                    scope.row.province +
                    scope.row.city +
                    scope.row.area +
                    scope.row.address
                  }}
                </template>
              </el-table-column>
            </el-table>
          </div>
        </el-collapse-transition>
      </div>
    </div>
    <br /><br />

    <!-- 新闻数据模块 -->
    <div class="news_data">
      <div class="right">
        <el-button @click="show2 = !show2">点击隐藏或展开新闻数据</el-button>
      </div>
      <div style="margin-top: 20px">
        <el-collapse-transition>
          <div v-show="show2">
            <el-table :data="news" style="width: 100%">
              <el-table-column prop="title" label="一句话新闻" align="center">
              </el-table-column>
            </el-table>
          </div>
        </el-collapse-transition>
      </div>
    </div>

    <!-- 模态框 -->
    <el-dialog
      v-if="dialogVisible"
      :title="choose + '上报'"
      :visible.sync="dialogVisible"
      width="70%"
    >
      <!-- 数据上报的模态框开头部分 -->
      <!-- 疫情上报模块 -->
      <!-- 本模块上传因没有可用接口使用本地数据，因此仅仅修改本地数据而不是提交from表单 -->
      <div class="viris_flex" v-if="choose === '疫情数据模块'">
        <el-input
          placeholder="请输入要上报的新增确诊人数"
          v-model="add_viris.new_confirm"
        >
        </el-input>
        <el-input
          placeholder="请输入要上报的新增治愈人数"
          v-model="add_viris.new_cure"
        >
        </el-input>
        <el-input
          placeholder="请输入要上报的新增死亡人数"
          v-model="add_viris.new_dead"
        >
        </el-input>
        <el-input
          placeholder="请输入要上报的现有确诊人数"
          v-model="add_viris.now_confirm"
        >
        </el-input>
        <el-input
          placeholder="请输入要上报的现有无症状人数"
          v-model="add_viris.now_nopain"
        >
        </el-input>
        <el-input
          placeholder="请输入要上报的外省输入人数"
          v-model="add_viris.now_other"
        >
        </el-input>
        <el-input
          placeholder="请输入要上报的累计确诊人数"
          v-model="add_viris.all_confirm"
        >
        </el-input>
        <el-input
          placeholder="请输入要上报的累计治愈人数"
          v-model="add_viris.all_cure"
        >
        </el-input>
        <el-input
          placeholder="请输入要上报的累计死亡人数"
          v-model="add_viris.all_dead"
        >
        </el-input>
      </div>

      <!-- 医院上报模块 -->
      <div class="hospital_css" v-if="choose === '医院数据模块'">
        <!-- :model用来绑定add_hospital对象，用来上传医院信息 -->
        <el-form
          :title="choose + '上报'"
          :model="add_hospital"
          :rules="hospital_rule"
          ref="add_hospital"
          label-width="100px"
        >
          <el-form-item label="医院名称" prop="name">
            <el-input
              class="input"
              style="width: 100%; height: 40px"
              placeholder="请输入具体信息"
              v-model="add_hospital.name"
              ref="add_hospital"
            >
            </el-input>
          </el-form-item>
          <el-form-item label="医院类型" prop="introduce">
            <el-input
              class="input"
              style="width: 100%; height: 40px"
              placeholder="请输入具体信息"
              v-model="add_hospital.introduce"
            >
            </el-input>
          </el-form-item>
          <el-form-item label="医院所在省份" prop="province">
            <el-input
              class="input"
              style="width: 100%; height: 40px"
              placeholder="请输入具体信息"
              v-model="add_hospital.province"
            >
            </el-input>
          </el-form-item>
          <el-form-item label="医院所在城市" prop="city">
            <el-input
              class="input"
              style="width: 100%; height: 40px"
              placeholder="请输入具体信息"
              v-model="add_hospital.city"
            >
            </el-input>
          </el-form-item>
          <el-form-item label="医院所在地区" prop="area">
            <el-input
              class="input"
              style="width: 100%; height: 40px"
              placeholder="请输入具体信息"
              v-model="add_hospital.area"
            >
            </el-input>
          </el-form-item>
          <el-form-item label="医院具体地址" prop="address">
            <el-input
              class="input"
              style="width: 100%; height: 40px"
              placeholder="请输入具体信息"
              v-model="add_hospital.address"
            >
            </el-input>
          </el-form-item>
        </el-form>
      </div>

      <!-- 新闻上报模块 -->
      <div
        style="display: flex; line-height: 20px; justify-content: center"
        v-if="choose === '新闻数据模块'"
      >
        <!-- :model用来绑定add_news对象，用来上传新闻信息 -->
        <el-form
          :title="choose + '上报'"
          :model="add_news"
          :rules="news_rule"
          label-width="100px"
          ref="add_news"
        >
          <el-form-item label="上报的新闻" prop="title">
            <el-input
              style="width: 100%; height: 40px"
              placeholder="请输入要上报的新闻"
              v-model="add_news.title"
            >
            </el-input>
          </el-form-item>
        </el-form>
      </div>
      <!-- 数据上报的模态框结尾部分,包括按钮 -->
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <!-- 三个不同按钮控制提交不同的模块 -->
        <el-button
          v-if="choose === '疫情数据模块'"
          type="primary"
          @click="submit_viris"
          >上报疫情数据</el-button
        >
        <el-button
          v-else-if="choose === '医院数据模块'"
          type="primary"
          @click="submit_hospital('add_hospital')"
          >上报医院数据</el-button
        >
        <el-button
          v-else-if="choose === '新闻数据模块'"
          type="primary"
          @click="submit_news('add_news')"
          >上报新闻数据</el-button
        >
      </span>
    </el-dialog>
  </div>
</template>

<script>
import { post, get } from "@/utils/request";
import * as echarts from "@/utils/echarts";
export default {
  data() {
    return {
      // 因疫情接口已经关闭，无法获取数据，因此采用本地数据测试
      // 疫情数据
      viris_data: {
        new_confirm: 8001,
        new_cure: 5071,
        new_dead: 4700,
        now_confirm: 10326,
        now_nopain: 7001,
        now_other: 6020,
        all_confirm: 14762,
        all_cure: 11007,
        all_dead: 10214,
      },
      // 医院数据
      hospital_data: [],
      // 新闻数据
      news: [],
      // 要上报的疫情数据
      add_viris: {
        new_confirm: "",
        new_cure: "",
        new_dead: "",
        now_confirm: "",
        now_nopain: "",
        now_other: "",
        all_confirm: "",
        all_cure: "",
        all_dead: "",
      },
      // 要上报的医院数据
      add_hospital: {
        // 不需要经纬度数据因此随意设置
        longitude: 666,
        latitude: 666,
        introduce: "",
        name: "",
        province: "",
        city: "",
        area: "",
        address: "",
      },

      // 要上报的新闻数据
      add_news: { title: "", content: "", categoryId: 3 },
      // 选择要上报的模块
      choose: "疫情数据模块",

      // 隐藏展示模块
      show3: true,
      show2: true,
      show1: true,

      // 模态框参数
      dialogVisible: false,

      // 新闻模态框验证规则
      news_rule: {
        title: [
          {
            required: true,
            message: "请输入新闻内容，不可为空!",
            trigger: "blur",
          },
        ],
      },

      // 医院模态框验证规则
      hospital_rule: {
        name: [
          {
            required: true,
            message: "请输入新闻内容，名称不可为空!",
            trigger: "blur",
          },
        ],
        introduce: [
          {
            required: true,
            message: "请输入新闻内容，类型不可为空!",
            trigger: "blur",
          },
        ],
        province: [
          {
            required: true,
            message: "请输入新闻内容，省份不可为空!",
            trigger: "blur",
          },
        ],
        city: [
          {
            required: true,
            message: "请输入新闻内容，城市不可为空!",
            trigger: "blur",
          },
        ],
        area: [
          {
            required: true,
            message: "请输入新闻内容，地区不可为空!",
            trigger: "blur",
          },
        ],
        address: [
          {
            required: true,
            message: "请输入新闻内容，地址不可为空!",
            trigger: "blur",
          },
        ],
      },
    };
  },
  created() {
    this.get_news();
    this.get_hospital();
  },
  mounted() {
    // 疫情柱状图自动生成
    this.initEcharts();
  },
  methods: {
    // // 用来获取疫情数据的接口，因为暂无可用接口改用本地数据，需要用其他接口也可以修改
    // async get_viris_data(){
    //   let res = await get();
    //   this.viris_data = res.data.data
    // },

    // 用来获取走马灯的文字和新闻信息
    async get_news() {
      // 设置参数
      let params = {
        page: 1,
        pageSize: 10,
      };
      // 发送请求
      let res = await get("/article/pageQuery", params);
      // console.log(res.data.list);
      this.news = res.data.list;
    },

    // 用来获取柳州医院的信息
    async get_hospital() {
      let str = {
        page: 1,
        pageSize: 100,
      };
      // 发送请求
      let res = await get("/hospital/pageQuery", str);
      // console.log(res.data.list);
      this.hospital_data = res.data.list;
    },

    // 下拉框内容获取函数
    handleCommand(command) {
      this.choose = command;
    },

    // 调用模态框函数，并接收选择的模块choose,并清空表单
    dialog() {
      this.dialogVisible = true;
    },

    // 外部引入的柱状图表
    initEcharts() {
      const option = {
        tooltip: {
          trigger: "axis",
          axisPointer: {
            type: "shadow",
            label: {
              backgroundColor: "#6a7985",
            },
          },
        },
        xAxis: {
          type: "category",
          data: [
            "新增确诊",
            "新增治愈",
            "新增死亡",
            "现存确诊",
            "现有无症状",
            "外省输入",
            "累积确诊",
            "累积治愈",
            "累积死亡",
          ],
        },
        yAxis: {
          type: "value",
        },
        series: [
          {
            data: [
              {
                value: this.viris_data.new_confirm,
                itemStyle: {
                  color: "#a90000",
                },
              },
              {
                value: this.viris_data.new_cure,
                itemStyle: {
                  color: "#059858",
                },
              },
              {
                value: this.viris_data.new_dead,
                itemStyle: {
                  color: "#2C2C2C",
                },
              },
              {
                value: this.viris_data.now_confirm,
                itemStyle: {
                  color: "#a90000",
                },
              },
              {
                value: this.viris_data.now_nopain,
                itemStyle: {
                  color: "#E3F717",
                },
              },
              {
                value: this.viris_data.now_other,
                itemStyle: {
                  color: "#3434E5",
                },
              },
              {
                value: this.viris_data.all_confirm,
                itemStyle: {
                  color: "#a90000",
                },
              },
              {
                value: this.viris_data.all_cure,
                itemStyle: {
                  color: "#059858",
                },
              },
              {
                value: this.viris_data.all_dead,
                itemStyle: {
                  color: "#2C2C2C",
                },
              },
            ],
            type: "bar",
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

    // 用于相加的函数

    // 上传疫情数据
    // 本模块上传因没有可用接口使用本地数据，因此仅仅修改本地数据而不是进行网络请求，因此刷新不会保存更改
    // 本方法较为累赘
    submit_viris() {
      this.dialogVisible = false;
      // 仅仅修改本地文件，有接口可以替换成网络请求
      // 没有接口处理数据库文件，要处理本地数据只能本地一条条相加
      if (this.add_viris.new_confirm === "") {
      } else {
        this.viris_data.new_confirm =
          parseInt(this.viris_data.new_confirm) +
          parseInt(this.add_viris.new_confirm);
      }
      if (this.add_viris.new_cure === "") {
      } else {
        this.viris_data.new_cure =
          parseInt(this.viris_data.new_cure) +
          parseInt(this.add_viris.new_cure);
      }
      if (this.add_viris.new_dead === "") {
      } else {
        this.viris_data.new_dead =
          parseInt(this.viris_data.new_dead) +
          parseInt(this.add_viris.new_dead);
      }
      if (this.add_viris.now_confirm === "") {
      } else {
        this.viris_data.now_confirm =
          parseInt(this.viris_data.now_confirm) +
          parseInt(this.add_viris.now_confirm);
      }
      if (this.add_viris.now_nopain === "") {
      } else {
        this.viris_data.now_nopain =
          parseInt(this.viris_data.now_nopain) +
          parseInt(this.add_viris.now_nopain);
      }
      if (this.add_viris.now_other === "") {
      } else {
        this.viris_data.now_other =
          parseInt(this.viris_data.now_other) +
          parseInt(this.add_viris.now_other);
      }
      if (this.add_viris.all_confirm === "") {
      } else {
        this.viris_data.all_confirm =
          parseInt(this.viris_data.all_confirm) +
          parseInt(this.add_viris.all_confirm);
      }
      if (this.add_viris.all_cure === "") {
      } else {
        this.viris_data.all_cure =
          parseInt(this.viris_data.all_cure) +
          parseInt(this.add_viris.all_cure);
      }
      if (this.add_viris.all_dead === "") {
      } else {
        this.viris_data.all_dead =
          parseInt(this.viris_data.all_dead) +
          parseInt(this.add_viris.all_dead);
      }

      // 提交时记得清空输入框
      this.add_viris.new_confirm = "";
      this.add_viris.new_cure = "";
      this.add_viris.new_dead = "";
      this.add_viris.now_confirm = "";
      this.add_viris.now_nopain = "";
      this.add_viris.now_other = "";
      this.add_viris.all_confirm = "";
      this.add_viris.all_cure = "";
      this.add_viris.all_dead = "";

      // console.log(this.viris_data);

      // 消息提示
      this.$message({
        type: "success",
        message: "上报成功！",
      });
      // 记得刷新柱状图
      this.initEcharts();
    },

    // 上传医院数据
    submit_hospital(hospital) {
      // 用来判断表单验证,防止输入不合规则却发送了请求
      this.$refs[hospital].validate((valid) => {
        if (valid) {
          // 深拷贝数据，防止跟着更新
          let res = { ...this.add_hospital };
          // 发送请求
          post("/hospital/saveOrUpdate", res).then((res) => {
            // 清空模态框中上次修改的数据
            this.add_hospital.name = "";
            this.add_hospital.introduce = "";
            this.add_hospital.province = "";
            this.add_hospital.city = "";
            this.add_hospital.area = "";
            this.add_hospital.address = "";
            // 重新刷新更新上报了的医院信息
            this.get_hospital();
            // 成功消息提示
            this.$message({
              type: "success",
              message: "上传成功",
            });
            this.dialogVisible = false;
          });
        } else {
          console.log("你不输入怎么给你上报?");
          return false;
        }
      });
    },

    // 上传新闻数据,参数拼接用post请求
    submit_news(news) {
      // 用来判断表单验证,防止输入不合规则却发送了请求
      this.$refs[news].validate((valid) => {
        if (valid) {
          // 深拷贝数据，防止跟着更新
          let res = { ...this.add_news };
          // 发送请求
          post("/article/saveOrUpdate", res).then((res) => {
            // 清空模态框中上次修改的数据
            this.add_news.title = "";
            this.add_news.content = "";
            this.add_news.categoryId = 3;
            // 重新刷新更新上报了的新闻信息
            this.get_news();
            // 成功消息提示
            this.$message({
              type: "success",
              message: "上传成功",
            });
            this.dialogVisible = false;
          });
        } else {
          console.log("你不输入怎么给你上报?");
          return false;
        }
      });
    },
  },
};
</script>

<style scoped>
/* 一句话新闻样式 */
.demonstration {
  font-size: 22px;
  color: black;
}
/* 顶部背景 */
.back {
  background-image: url("../../assets/background.jpg");
  width: 100%;
  color: white;
  border-radius: 25px;
  font-size: 50px;
  font-weight: bolder;
  text-align: center;
  line-height: 160px;
  border: 3px solid black;
  margin-bottom: 20px;
}
/* 走马灯字体样式 */
.light_title {
  font-size: 20px;
  text-align: center;
  color: rgb(65, 70, 68);
}
/* 折叠部分的样式 */
.transition-box {
  margin-bottom: 10px;
  width: 200px;
  height: 100px;
  border-radius: 4px;
  background-color: #409eff;
  text-align: center;
  color: #fff;
  padding: 40px 20px;
  box-sizing: border-box;
  margin-right: 20px;
}
/* 下拉款和按钮布局 */
.select {
  display: flex;
}
.bto {
  margin-left: 20px;
}
/* 设置截止时间的样式 */
.realTime {
  margin: 5px 0;
  color: #999999;
  font-size: 20px;
}
/* 隐藏按钮居右侧显示 */
.right {
  display: flex;
  flex-direction: row-reverse;
}
/* 疫情模态框样式 */
.viris_flex {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
}
/* 疫情模态框中的输入框样式 */
.viris_flex .el-input {
  width: 33%;
  height: 80px;
}
/* 医院模态框中输入框的样式 */
.hospital_css .input {
  height: 100px;
}
</style>
