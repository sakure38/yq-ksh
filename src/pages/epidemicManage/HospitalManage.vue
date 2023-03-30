<!--
 * @Author: root 2971301532@qq.com
 * @Date: 2022-12-22 07:37:55
 * @LastEditors: root 2971301532@qq.com
 * @LastEditTime: 2022-12-22 20:24:31
 * @FilePath: \practical-training-i-project\src\pages\epidemicManage\HospitalManage.vue
 * @Description: 这是默认设置,请设置`customMade`, 打开koroFileHeader查看配置 进行设置: https://github.com/OBKoro1/koro1FileHeader/wiki/%E9%85%8D%E7%BD%AE
-->
<template>
  <div class="hospitalManage">
    <!-- 顶部区域 -->
    <!-- 顶部区域只放一个按钮组件 -->
    <div class="topArea">
      <!-- 新增医院数据按钮 -->
      <el-button type="primary" @click="addHospitalMassage()">新增数据<i class="el-icon-upload el-icon--right"></i></el-button>
    </div>
    <!-- 表格区域 -->
    <div class="content">
      <el-table :data="hospitalData" stripe style="width: 100%">
        <!-- 序号 -->
        <el-table-column type="index" label="序号" align="center">
        </el-table-column>
        <!-- 医院名称 -->
        <el-table-column prop="name" label="医院名称" align="center">
        </el-table-column>
        <!-- 医院简介 -->
        <el-table-column prop="introduce" label="医院简介" align="center">
        </el-table-column>
        <!-- 医院地址 -->
        <el-table-column label="医院地址" align="center">
          <!-- 作用域插槽赋值 -->
          <template slot-scope="scope">
            {{
              scope.row.province +
              scope.row.city +
              scope.row.area +
              scope.row.address
            }}
          </template>
        </el-table-column>
        <!-- 介绍 -->
        <!-- 使用show-overflow-tooltip属性省略 -->
        <el-table-column
          prop="flags"
          show-overflow-tooltip="true"
          label="介绍"
          align="center"
        >
        </el-table-column>
        <!-- 经度 -->
        <el-table-column prop="longitude" label="经度" align="center">
        </el-table-column>
        <!-- 纬度 -->
        <el-table-column prop="latitude" label="纬度" align="center">
        </el-table-column>
        <!-- 操作 -->
        <el-table-column label="操作" align="center">
          <template slot-scope="scope">
            <el-button type="primary" @click="changeHospitalMassage(scope.row)" icon="el-icon-edit" circle></el-button>
            <el-button type="danger" @click="deleteHospitalMassage(scope.row)" icon="el-icon-delete" circle></el-button>
          </template>
        </el-table-column>
      </el-table>
    </div>
    <!-- 分页区域 -->
    <div class="page" style="text-align: right">
      <div class="block">
        <span class="demonstration"></span>
        <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page.sync="currentPage2"
          :page-sizes="[2, 4, 6, 8]"
          :page-size="100"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total"
          background
        >
        </el-pagination>
      </div>
    </div>
    <!-- 模态框区域 -->
    <el-dialog
      v-if="dialogVisible"
      :visible.sync="dialogVisible"
      width="30%"
      :title="dialogTitle"
    >
      <!-- 表单区域开始处 -->
      <el-form
        ref="hospitalForm"
        :model="hospitalForm"
        :rules="hospitalRule"
        label-width="80px"
      >
        <!-- 名称 -->
        <el-form-item label="医院名称" prop="name">
          <el-input v-model="hospitalForm.name"></el-input>
        </el-form-item>
        <!-- 简介 -->
        <el-form-item label="简介" prop="introduce">
          <el-input v-model="hospitalForm.introduce"></el-input>
        </el-form-item>
        <!-- 省市区选择（级联选择器） -->
        <el-form-item label="省市区" prop="pca">
          <el-cascader
            v-model="hospitalForm.pca"
            :props="{ label: 'name', value: 'name' }"
            :options="pca"
            style="width: 80%"
          ></el-cascader>
        </el-form-item>
        <!-- 详细地址 -->
        <el-form-item label="详细地址" prop="address">
          <el-input v-model="hospitalForm.address"></el-input>
        </el-form-item>
        <!-- 介绍 -->
        <el-form-item label="介绍" prop="flags">
          <el-input type="textarea" v-model="hospitalForm.flags"></el-input>
        </el-form-item>
        <!-- 经度 -->
        <el-form-item label="经度" prop="longitude">
          <el-input v-model="hospitalForm.longitude"></el-input>
        </el-form-item>
        <!-- 纬度 -->
        <el-form-item label="纬度" prop="latitude">
          <el-input v-model="hospitalForm.latitude"></el-input>
        </el-form-item>
      </el-form>
      <!-- 表单区域结束处 -->
      <span slot="footer" class="dialog-footer">
        <!-- 取消 -->
        <el-button @click="dialogVisible = false" type="danger" icon="el-icon-close" circle></el-button>
        <!-- 确定 -->
        <el-button @click="submitHospitalData('hospitalForm')" type="primary" icon="el-icon-check" circle></el-button>
      </span>
    </el-dialog>
    
  </div>
</template>

<script>
// 引入axios
import { get, post } from "@/utils/request";
// 引入省市区的pca.json数据
import pca from "@/assets/pca.json";
export default {
  data() {
    return {
      // 医院数据
      hospitalData: [],
      // 当前页码
      page: 1,
      // 当前每页大小
      pageSize: 2,
      // 页面总数
      total: 2,
      // 模态框是否显示
      dialogVisible: false,
      // 省市区的pca数据
      pca,
      // 医院数据表单
      hospitalForm: {},
      // 医院表单验证规则
      hospitalRule: {
        name: [
          { required: true, message: "请输入医院名称", trigger: "blur" },
          {
            min: 3,
            max: 20,
            message: "长度在 3 到 20 个字符",
            trigger: "blur",
          },
        ],
        flags: [{ required: true, message: "请输入介绍", trigger: "blur" }],
        address: [
          { required: true, message: "请输入详细地址", trigger: "blur" },
        ],
        pca:[
          {required: true, message: "请选择省市区", trigger: "blur"}
        ],
        longitude: [{ required: true, message: "请输入经度", trigger: "blur" }],
        latitude: [{ required: true, message: "请输入纬度", trigger: "blur" }],
      },
    };
  },
  created() {
    this.getHospitalData();
  },
  methods: {
    // 获取定点医院数据
    async getHospitalData() {
      // 设置参数
      let params = {
        page: this.page,
        pageSize: this.pageSize,
      };
      // 发送请求
      let res = await get("/hospital/pageQuery", params);
      this.hospitalData = res.data.list;
      this.total = res.data.total;
    },
    // 控制每页大小
    handleSizeChange(val) {
      // 调整当前页面大小
      this.pageSize = val;
      // 刷新数据
      this.getHospitalData();
    },
    // 控制当前页数
    handleCurrentChange(val) {
      // 调整当前页码
      this.page = val;
      // 刷新数据
      this.getHospitalData();
    },
    // 新增医院信息
    addHospitalMassage() {
      // 清空表单
      this.hospitalForm = {};
      // 设置标题
      this.dialogTitle = "新增医院信息";
      this.dialogVisible = true;
    },
    // 修改医院信息
    changeHospitalMassage(row) {
      // 设置标题
      this.dialogTitle = "修改医院信息";
      // 使用拓展运算符进行深拷贝
      this.hospitalForm = { ...row };
      // 将省市区数据显示
      this.hospitalForm.pca = [row.province, row.city, row.area];

      this.dialogVisible = true;
    },
    // 删除医院信息
    deleteHospitalMassage(row) {
      this.$confirm("此操作将永久删除该医院信息, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
        .then(() => {
          get('/hospital/deleteById',{id:row.id}).then(res =>{
            this.$message({
              type:'success',
              message:res.message
            })
            // 页码超出小bug的修复
            // 计算当前的总页数
            let totalPage = Math.ceil((this.total-1)/this.pageSize)
            // 通过判断当前页码是否大于总页数来确定当前是否超出页码
            let currentPage = this.page > totalPage ? totalPage : this.page
            // 判断currentPage是否大于0
            this.page = currentPage > 0 ? currentPage : 1
            // 刷新当前数据
            this.getHospitalData()
          })
          alert("已成功删除信息")
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消删除",
          });
        });
    },
    // 保存或更新数据
    submitHospitalData(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          // 深拷贝赋值参数
          let data = { ...this.hospitalForm };
          // 给省市区声母并赋值
          data.province = this.hospitalForm.pca[0];
          data.city = this.hospitalForm.pca[1];
          data.area = this.hospitalForm.pca[2];
          // 删除data中的pca
          delete data.pca;
          post("/hospital/saveOrUpdate", data).then((res) => {
            // 弹出成功提示
            this.$message({
              type: "success",
              message: res.message,
            });
            // 刷新当前页面
            this.getHospitalData();
            // 清空表单
            this.hospitalForm = {};
            // 关闭模态框
            this.dialogVisible = false;
          });
          alert("保存数据成功");
        } else {
          alert("保存数据失败");
          console.log("error submit!!");
          return false;
        }
      });
    },
  },
};
</script>

<style scoped>
.content {
  height: 80vh;
  width: 100%;
  background-color: #fafafa;
}
.page {
  /* height: 10vh; */
  width: 100%;
}
</style>
