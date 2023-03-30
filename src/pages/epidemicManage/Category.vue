<!--
 * @Author: 'JIU' 3157123393@qq.com
 * @Date: 2022-12-12 08:47:20
 * @LastEditors: 'JIU' 3157123393@qq.com
 * @LastEditTime: 2022-12-22 20:13:44
 * @FilePath: \V14\epidemic-visualization\src\pages\epidemicManage\Category.vue
 * @Description: 这是默认设置,请设置`customMade`, 打开koroFileHeader查看配置 进行设置: https://github.com/OBKoro1/koro1FileHeader/wiki/%E9%85%8D%E7%BD%AE
-->
!<template>
  <div class="catgry">
    <div class="topArea">
      <el-button type="primary" size="small" @click="addHandler">新增</el-button>
    </div>
    <!--列表-->
    <div class="content">
      <el-table :data="categoryData" style="width:100%">
        <el-table-column type="index" label="序号" align="center"></el-table-column>        
        <el-table-column prop="name" label="分类名称" align="center"></el-table-column>
        <el-table-column prop="description" label="分类简介" align="center"></el-table-column>
        <el-table-column label="操作" align="center">
          <template slot-scope="scope">
            <el-button @click="editHandler(scope.row)" type="text" size="small">修改</el-button>
            <el-button @click="deleteHandler(scope.row)" type="text" size="small">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
    </div>
    <!-- 模态框 -->
    <el-dialog
      v-if="dialogVisible"
      :title="dialogTitle"
      :visible.sync="dialogVisible"
      width="70%"
    >
      <!-- 表单区域 开始 -->
      <el-form ref="form" :model="categoryForm" label-width="80px">
        <el-form-item label="分类名称">
          <el-input v-model="categoryForm.name"></el-input>
        </el-form-item>
        <!-- 资讯详情 -->
        <el-form-item label="分类简介">
          <!-- 3.使用富文本编辑器 -->
          <Tinymce v-model="categoryForm.description" :height="240"></Tinymce>
        </el-form-item>
      </el-form>
      <!-- 表单区域 结束 -->
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="submitHandler">提 交</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
import {get,post} from '@/utils/request';
import Tinymce from "@/components/Tinymce";
export default {
  components:{
    Tinymce,
  },
  data() {
    return {
      categoryData: [],
      dialogTitle:"",
      dialogVisible:false,
      categoryForm:{},//对象
    }
  },
  created() {
    this.getCategoryData()
  },
  methods: {
    async getCategoryData(){

      let res = await get('/category/findAll')
    //  console.log(res.data.data);
      this.categoryData = res.data;
    },
    addHandler() {
      // 清空表单对象
      this.categoryForm = {};
      // 设置模态框的标题
      this.dialogTitle = "增加资讯分类";
      // 打开模态框
      this.dialogVisible = true;
    },
    // 修改新闻数据（分类信息
    editHandler(row) {
      // 数据赋值
      this.categoryForm = { ...row };
      // 设置模态框的标题
      this.dialogTitle = "修改分类信息";
      // 打开模态框
      this.dialogVisible = true;
    },
    // 保存或更新资讯数据
    submitHandler() {
      // 设置参数
      let data = { ...this.categoryForm };
      // console.log(data);
      // 发送请求
      post("/category/saveOrUpdate", data).then((res) => {
        // 消息提示
        this.$message({
          type: "success",
          message: res.message,
        });
        // 清空模态框
        this.categoryForm = {};
        // 刷新页面数据
        this.getCategoryData();
        // 关闭模态框
        this.dialogVisible = false;
      });
    },
    // 根据id删除资讯分类
    deleteHandler(row) {
      // 删除的消息提示框
      this.$confirm("此操作将永久删除该资讯分类, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
        // 点击确定按钮 执行的是.then里面的处理函数
        .then(() => {
          // 发送请求
          get("/category/deleteById", { id: row.id }).then((res) => {
            // 消息提示
            this.$message({
              type: "success",
              message: res.message,
            });
            // 处理页码问题 因为当前页面目前没有使用分页 暂时不处理
            // 刷新当前页面数据
            this.getCategoryData();
          });
        })
        // 点击取消按钮 执行的是.catch里面的处理函数
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消删除",
          });
        });
    },
    
  },
}
</script>

<style lang="scss" scoped>

</style>