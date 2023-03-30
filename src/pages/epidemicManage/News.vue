<!--
 * @Author: nimingzhi 1286832074@qq.com
 * @Date: 2022-12-12 09:47:57
 * @LastEditors: nimingzhi 1286832074@qq.com
 * @LastEditTime: 2022-12-22 20:28:28
 * @FilePath: /epidemic-visualization/src/pages/epidemicManage/News.vue
 * @Description: 这是默认设置,请设置`customMade`, 打开koroFileHeader查看配置 进行设置: https://github.com/OBKoro1/koro1FileHeader/wiki/%E9%85%8D%E7%BD%AE
-->
<template>
  <div class="news">
    <!-- 顶部按钮 -->
    <div class="topArea">
      <el-button v-if="value == 0" disabled type="primary" @click="addnews"
        >新增</el-button
      >
      <el-button v-if="value == 100" type="primary" @click="addnews"
        >新增</el-button
      >
      <!-- 这是一个滑块组件 可以控制编辑状态 -->
      不可编辑
      <el-tooltip content="设置状态" placement="top">
        <el-switch
          v-model="value"
          active-color="#13ce66"
          inactive-color="#ff4949"
          active-value="100"
          inactive-value="0"
        >
        </el-switch>
      </el-tooltip>
      可编辑
    </div>
    <!-- 中间列表展示区域 -->
    <div class="content">
      <el-table :data="newData" style="width: 100%">
        <!-- 序号-->
        <!-- 这里要看一下为什么要用index -->
        <el-table-column type="index" label="序号" width="180" align="center">
        </el-table-column>
        <!-- 新闻标题 -->
        <el-table-column
          prop="title"
          label="新闻标题"
          width="600"
          align="center"
        >
        </el-table-column>
        <!-- 新闻分类 -->
        <el-table-column prop="category.name" label="新闻分类" align="center">
        </el-table-column>
        <!-- 发布时间 -->
        <el-table-column label="发布时间">
          <!-- 要使用作用域插槽为每一条的时间赋值 -->
          <template slot-scope="scope">
            {{ scope.row.publishTime | fmtDate }}
          </template>
        </el-table-column>
        <!-- 操作 -->
        <el-table-column label="操作" align="center">
          <template slot-scope="scope">
            <el-button
              type="primary"
              v-if="value == 0"
              disabled
              icon="el-icon-edit"
              @click="addHandler(scope.row)"
              >修改</el-button
            >
            <el-button
              type="primary"
              v-if="value == 0"
              disabled
              @click="deleteHandler(scope.row)"
              >删除<i class="el-icon-delete el-icon--right"></i>
            </el-button>
            <el-button
              type="primary"
              v-if="value == 100"
              icon="el-icon-edit"
              @click="addHandler(scope.row)"
              >修改</el-button
            >
            <el-button
              type="primary"
              v-if="value == 100"
              @click="deleteHandler(scope.row)"
              >删除<i class="el-icon-delete el-icon--right"></i>
            </el-button>
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
      <el-form ref="form" :model="newsForm" label-width="80px">
        <!-- 新闻标题 -->
        <el-form-item label="新闻标题">
          <el-input v-model="newsForm.title" @click="next"></el-input>
        </el-form-item>
        <!-- 新闻分类 -->
        <el-form-item label="新闻分类">
          <el-select v-model="newsForm.categoryId" placeholder="请选择活动区域" @click="next">
            <!--
              label 是要展示在用户面前的数据
              value 就是要传递给后端的参数
             -->
            <el-option
              v-for="item in categoryData"
              :key="item.id"
              :label="item.name"
              :value="item.id"
            ></el-option>
          </el-select>
        </el-form-item>
        <!-- 资讯详情 -->
        <el-form-item label="资讯详情">
          <!-- 3.使用副文本编辑器 -->
          <Tinymce v-model="newsForm.content" :height="240"></Tinymce>
        </el-form-item>
      </el-form>
      <!-- 表单区域 结束 -->
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="submitHandler">提 交</el-button>
      </span>
    </el-dialog>
    <!-- 底部分页区域 -->
    <div class="page" style="text-align: right">
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page.sync="page"
        :page-sizes="[2, 4, 6, 8]"
        :page-size="pageSize"
        layout="sizes, prev, pager, next, total"
        :total="total"
      >
      </el-pagination>
      <!-- 这是一个评分的小组件 -->
      <div class="rate">
        对本界面使用体验怎么样？
        <el-rate v-model="rate" show-text> </el-rate>
      </div>
    </div>
  </div>
</template>

<script>
import { get, post } from "@/utils/request";
import Tinymce from "@/components/Tinymce";
export default {
  // 声明要使用的组件
  components: {
    Tinymce,
  },
  data() {
    return {
      //设置滑块的初始值为100
      value: "100",
      //将评分小组件的value值设置为null
      rate: null,
      //得到获取的新闻数据
      newData: [],
      // 新闻新增信息表单对象
      newsForm: {},
      //设置一个模态框，控制显示与隐藏
      dialogVisible: false,
      //栏目数据
      categoryData: [],
      //模态框的标题线设置为空
      dialogTitle: "",
      //默认当前页数为1
      page: 1,
      //默认每页展示多少条数据
      pageSize: 4,
      //默认数据的总条数
      total: 10,
    };
  },
  created() {
    this.getNewsData();
    this.getTypeData();
  },
  methods: {
    //获取所有新闻的数据
    async getNewsData() {
      //配置需要的参数
      let params = {
        page: this.page,
        pageSize: this.pageSize,
      };
      //发送请求
      let res = await get("/article/pageQuery", params);
      //先获取全部数据，再筛选出我们需要的信息
      console.log(res);
      this.newData = res.data.list;
      //将数据的总条数赋值给total
      this.total = res.data.total;
    },
    // 获取分类信息的数据
    async getTypeData() {
      //发送请求
      let res = await get("/category/findAll");
      //先获取全部数据，再筛选出我们需要的信息
      // console.log(res);
      this.categoryData = res.data;
    },
    //增加新闻数据
    addnews() {
      //第一步先清空表单，防止还有缓存在上面
      this.newsForm = {};
      // 设置模态框的标题
      this.dialogTitle = "修改新闻资讯";
      // 打开模态框
      this.dialogVisible = true;
    },
    // 此函数为保存或者更新资讯数据
    submitHandler() {
      //设置所需要的参数
      let data = { ...this.newsForm };
      //先获取看一下有哪些信息
      // console.log(data)
      // 发送请求
      post("/article/saveOrUpdate", data).then((res) => {
        //信息提示
        this.$message({
          type: "success",
          message: res.message,
        });
        //清空模态框
        this.newsForm = {};
        //刷新
        this.getNewsData();
        //关闭模态框
        this.dialogVisible = false;
      });
    },
    //因为id是唯一的，所以删除的时候根据id来删除
    deleteHandler(row) {
      //弹出消息提示框
      this.$confirm("这个操作将永久删除该咨询，是否继续？", "提示", {
        // 两个按键
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
        //点击确定按钮，执行的是.then里面的函数
        .then(() => {
          //发送请求
          get("/article/deleteById", { id: row.id }).then((res) => {
            //消息提示
            this.$message({
              type: "success",
              message: res.message,
            });
            //这里是处理页码问题的一个算法，让删除数据不会出现已经删除了，但是页面还没有-1
            // 第一步计算出当前数据的总页数，用totalPage接收
            let totalPage = Math.ceil((this.total - 1) / this.pageSize);
            // 第二步如果修改的数据是本页的最后一条数据，那么要使得删除之后就查询上一页的数据
            // 如果this.page大于totalPage，则currentPage为totalPage，否则还是为原来的this.page
            let currentPage = this.page > totalPage ? totalPage : this.page;
            // 第三步如果只剩下一页，就直接显示第一页的数据就可以了
            this.page = currentPage < 1 ? 1 : currentPage;
            //刷新当前页面数据
            this.getNewsData();
          });
        })
        //点击取消按钮，执行的是下面.catch里面的处理函数
        .catch(() => {
          this.$message({
            type: "info",
            message: "已经取消删除",
          });
        });
    },
    //修改新闻的数据
    addHandler(row) {
      //将打开的模态框进行复制展示给用户
      this.newsForm = { ...row };
      // 设置标题
      this.dialogTitle = "修改新闻信息";
      //打开模态框
      this.dialogVisible = true;
    },
    // 此函数控制每一页显示多少条数据
    handleSizeChange(val) {
      // 每当每一页数据的条数被手动选择改变时，要实时将data中的pageSize也进行更新
      this.pageSize = val;
      // 刷新一下getNewsData()方法，重新获得数据
      this.getNewsData();
    },
    // 此函数为控制当前是第几页
    handleCurrentChange(val) {
      // 页数发生改变时，要实时更新data中的page
      this.page = val;
      // 刷新一下getNewsData()方法，重新获得数据
      this.getNewsData();
    },
    //步骤函数
          next() {
        if (this.active++ > 2) 
        this.active = 0;
      }
  },
};
</script>
<style scoped>
.rate {
  text-align: center;
}
</style>