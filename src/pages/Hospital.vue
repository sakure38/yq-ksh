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
 * @Date: 2022-12-12 19:28:09
 * 文件路径:
 * @FilePath: \web课设d:\实训1\实训一项目\src\pages\Hospital.vue
 -->

<template>
  <div>
    <div class="hospital">
      <!-- 顶部背景 -->
      <div class="background">医疗救治定点医院和发热门诊一览</div>
      <!-- 搜索栏 -->
      <div class="search" style="margin: 20px 0">
        <!-- 输入框 -->
        <el-input
          style="width: 30%"
          placeholder="请输入内容"
          v-model="input"
          clearable
        >
        </el-input>
        <!-- 按钮 -->
        <el-button type="primary" @click="get_hospital">搜索</el-button>
        &nbsp;&nbsp;&nbsp;
        <!-- 医院资源按钮区域 -->
        <el-button type="primary" @click="addFolder()">医疗报号</el-button>
        &nbsp;&nbsp;&nbsp;
        <el-button type="primary" @click="cxzt()">查询状态</el-button
        ><i>(再点一次回定位界面)</i>
        <el-dialog
          :visible.sync="dialogVisible"
          width="40%"
          @open="onOpen"
          @close="onClose"
          title="医疗咨询"
        >
          <el-row :gutter="15">
            <el-form
              ref="elForm"
              :model="formData"
              :rules="rules"
              size="medium"
              label-width="150px"
            >
              <el-col :span="20">
                <el-form-item label="咨询医院" prop="title">
                  <el-select
                    v-model="formd.title"
                    placeholder="请下拉选择咨询医院"
                    clearable
                    :style="{ width: '100%' }"
                  >
                    <el-option
                      v-for="(item, index) in hospitolOptions"
                      :key="index"
                      :label="item.label"
                      :value="item.value"
                      :disabled="item.disabled"
                    ></el-option>
                  </el-select>
                </el-form-item>
              </el-col>
              <el-col :span="20">
                <el-form-item label="电话号码" prop="categoryId">
                  <el-input
                    v-model="formd.categoryId"
                    placeholder="请输入电话号码"
                    clearable
                    :style="{ width: '100%' }"
                  >
                  </el-input>
                </el-form-item>
              </el-col>
              <el-col :span="20">
                <el-form-item label="姓名" prop="authorId">
                  <el-input
                    v-model="formd.authorId"
                    placeholder="请输入姓名"
                    clearable
                    :style="{ width: '100%' }"
                  ></el-input>
                </el-form-item>
              </el-col>
              <el-col :span="20">
                <el-form-item label="身份证号" prop="id">
                  <el-input
                    v-model="formd.id"
                    placeholder="请输入身份证号"
                    clearable
                    :style="{ width: '100%' }"
                  >
                  </el-input>
                </el-form-item>
              </el-col>
              <el-col :span="20">
                <el-form-item label="病况描述" prop="content">
                  <el-input
                    v-model="formd.content"
                    type="textarea"
                    placeholder="请输入病况描述"
                    :autosize="{ minRows: 4, maxRows: 4 }"
                    :style="{ width: '100%' }"
                  ></el-input>
                </el-form-item>
              </el-col>
            </el-form>
          </el-row>
          <div slot="footer">
            <el-button @click="close">取消</el-button>
            <el-button type="primary" @click="handelConfirm('elForm')"
              >确定</el-button
            >
          </div>
        </el-dialog>
      </div>
      <!-- 医院列表展示区 -->
      <div v-show="show2">
        <el-card class="box-card">
          <div slot="header" class="clearfix">
            <span>报号信息</span>
            <el-button style="float: right; padding: 3px 0" type="text"
              ></el-button
            >
          </div>
          <div>
            {{"医院-------" + formd.title }}<br>
            {{"电话号码-------" + formd.categoryId }}<br>
            {{"姓名-------" + formd.authorId }}<br>
            {{"身份证-------   " + formd.id }}<br>
            {{"病况描述------- " + formd.content }}

          </div>
          <el-steps :space="200" :active="1" finish-status="success" class="tt">
            <el-step title="报号"></el-step>
            <el-step title="排队中"></el-step>
            <el-step title="报号成功"></el-step>
          </el-steps>
        </el-card>
      </div>
      <div
        class="list"
        @click="to_map(item)"
        v-for="(item, index) in hospital_data"
        :key="item.id"
        v-show="show1"
      >
        <!-- 医院信息 -->
        <div class="list_left">
          <div class="name">
            <div class="num">{{ index + 1 }}</div>
            {{ item.name }}
          </div>
          <div class="data">{{ item.flags }}</div>
          <div class="address">
            {{ item.province + item.city + item.area + item.address }}
          </div>
        </div>
        <div class="list_right">
          <div class="area">
            <div class="icon">
              <img src="../assets/dingwei.png" width="30px" alt="这是图标" />
            </div>
            <div class="where">{{ item.how_far }}KM</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { get, post } from "@/utils/request";
export default {
  data() {
    return {
      // 用户输入的医院
      input: "",
      hospital_data: [],
      // 咨询按钮属性
      show1: true,
      show2: false,
      dialogVisible: false,
      formd: {},
      formData: {
        hospitol: "",
        phone: "",
        name: "",
        sfz: "",
        bkms: "",
      },
      rules: {
        hospitol: [
          {
            required: true,
            message: "请下拉选择咨询医院",
            trigger: "change",
          },
        ],
        phone: [
          {
            required: true,
            message: "请输入电话号码",
            trigger: "blur",
          },
        ],
        name: [
          {
            required: true,
            message: "请输入姓名",
            trigger: "blur",
          },
        ],
        sfz: [
          {
            required: true,
            message: "请输入身份证号",
            trigger: "blur",
          },
        ],
        bkms: [
          {
            required: true,
            message: "请输入病况描述",
            trigger: "blur",
          },
        ],
      },
      hospitolOptions: [
        {
          label: "柳州市工人医院",
          value: "柳州市工人医院",
        },
        {
          label: "柳州市中医院",
          value: "柳州市中医院",
        },
        {
          label: "柳州人民医院",
          value: "柳州人民医院",
        },
        {
          label: "柳州市妇幼保健院",
          value: "柳州市妇幼保健院",
        },
        {
          label: "广西壮族自治区脑科医院",
          value: "广西壮族自治区脑科医院",
        },
        {
          label: "柳州市柳铁中心医院",
          value: "柳州市柳铁中心医院",
        },
      ],
    };
  },
  created() {
    this.get_hospital();
    this.get_location();
  },
  methods: {
    //咨询按钮方法
    cxzt() {
      this.show1 = !this.show1;
      this.show2 = !this.show2;
    },
    addFolder() {
      this.dialogVisible = true;
    },
    onOpen() {},
    onClose() {
      this.$refs["elForm"].resetFields();
    },
    close() {
      this.dialogVisible = false;
    },
    handelConfirm(elForm) {
      this.$refs[elForm].validate((valid) => {
        if (!valid) {
          let data = { ...this.formd };
          post("/article/saveOrUpdate", data).then((res) => {
            this.$message({
              type: "success",
              message: "上传成功",
            });
            this.dialogVisible = false;
          });
          return;
        }
        this.close();
      });
    },
    // 获取医院相关信息
    async get_hospital() {
      // 参数设置
      let str = {
        page: 1,
        pageSize: 100,
        // 根据医院名称定义
        name: this.input,
      };
      // 发送请求
      let res = await get("/hospital/pageQuery", str);
      // console.log(res.data.list);
      this.hospital_data = res.data.list;
      // 这个很重要，是用来保存this的指向，方便其他函数调用当前函数变量的！
      // console.log(this.hospital_data);
    },
    // 获取医院和目前位置的距离
    get_location() {
      let save_this = this;
      AMap.plugin("AMap.Geolocation", function () {
        var geolocation = new AMap.Geolocation({
          // 是否使用高精度定位，默认：true
          enableHighAccuracy: true,
          // 设置定位超时时间，默认：无穷大
          timeout: 10000,
          // 定位按钮的停靠位置的偏移量
          offset: [10, 20],
          //  定位成功后调整地图视野范围使定位位置及精度范围视野内可见，默认：false
          zoomToAccuracy: true,
          //  定位按钮的排放位置,  RB表示右下
          position: "RB",
        });

        geolocation.getCurrentPosition(function (status, result) {
          if (status == "complete") {
            onComplete(result);
          } else {
            onError(result);
          }
        });

        function onComplete(data) {
          // data是具体的定位信息
          // 成功读取后输出位置
          // console.log(data);

          // 调试能否读取到数据
          // console.log(save_this.hospital_data);
          // 获取当前每个医院的经纬度
          save_this.hospital_data.map((item) => {
            let my_str = [data.position.lng, data.position.lat];
            let hospital_str = [item.longitude, item.latitude];
            var res = AMap.GeometryUtil.distance(my_str, hospital_str);
            // 计算经纬度距离
            // console.log((res / 1000).toFixed(2));
            // 使用一个Vue中的API $set(目标对象,'要新增的属性的名字',要新增的属性的值)
            save_this.$set(item, "how_far", (res / 1000).toFixed(2));
          });
        }

        function onError(data) {
          // 定位出错
        }
      });
    },
    // 页面的跳转
    to_map(item) {
      this.$router.push({
        path: "/hospital/map",
        query: item,
      });
    },
  },
};
</script>

<style scoped>
.background {
  background-image: url("../assets/10.jpg");
  /* background-image: '../assets/bg1.png'; */
  background-size: 100% 100%;
  line-height: 250px;
  color: white;
  font-size: 24px;
  font-weight: bolder;
  /* 圆角边框 */
  border-radius: 20px;
  /* 左内边距 */
  padding-left: 35%;
}
.list {
  display: flex;
  border-bottom: 1px solid #f8f8f8;
  margin-top: 10px;
}
.list_left {
  flex: 1;
}
.list_left .num {
  /* 切换为行内元素，同一行显示需要是行内元素 */
  display: inline-block;
  background-color: burlywood;
  width: 20px;
  height: 20px;
  line-height: 20px;
  /* 圆形边框的使用，请记住 */
  border-radius: 50%;
  text-align: center;
}
.list_left .name {
  font-size: 20px;
}
.list_left .data {
  /* 切换为行内元素，背景大小随着字体变化 */
  background-color: #ffb34e;
  padding: 0.2em;
  display: inline-block;
  color: white;
  border-radius: 5px;
  margin: 5px 0;
  width: 95%;
}
.list_left .address {
  color: #999;
  margin-bottom: 5px;
}
.area {
  text-align: center;
}
.tt{
  margin-top: 20px;
}
</style>
