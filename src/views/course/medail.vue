<template>
  <div>
    <el-card class="box-card">
      <div slot="header" class="clearfix">
        <el-button type="primary" icon="el-icon-edit" @click="addMedail"
          >新增图文</el-button
        >
        <div class="right">
          <el-select v-model="value" placeholder="商品状态" class="select">
            <el-option
              v-for="item in options"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            >
            </el-option>
          </el-select>
          <el-input v-model="input" placeholder="标题" class="input"></el-input>
          <el-button type="primary" icon="el-icon-search">搜索</el-button>
        </div>
      </div>
      <div class="text item">
        <el-table :data="tableData" border style="width: 100%">
          <el-table-column
            prop="id"
            label="ID"
            width="80"
            sortable
            header-align="center"
            align="center"
          ></el-table-column>
          <el-table-column prop="text" label="图文内容">
            <template slot-scope="scope">
              <div class="coverText">
                <img :src="scope.row.cover" alt="" />
                <div class="priceText">
                  <span>{{ scope.row.title }}</span>
                  <span class="price">￥{{ scope.row.t_price }}</span>
                </div>
              </div>
            </template>
          </el-table-column>
          <el-table-column
            prop="sub_count"
            label="订阅量"
            width="110"
            header-align="center"
            align="center"
          ></el-table-column>
          <el-table-column
            label="状态"
            width="110"
            header-align="center"
            align="center"
          >
            <template slot-scope="scope">
              <el-tag type="success" v-show="scope.row.status === 1"
                >已上架</el-tag
              >
              <el-tag type="danger" v-show="scope.row.status === 0"
                >已下架</el-tag
              >
            </template>
          </el-table-column>
          <el-table-column
            prop="created_time"
            label="创建时间"
            width="150"
            header-align="center"
            align="center"
          ></el-table-column>
          <el-table-column
            label="操作"
            width="250"
            header-align="center"
            align="center"
          >
            <template slot-scope="scope">
              <el-button
                size="mini"
                type="primary"
                @click="handleEdit(scope.$index, scope.row)"
                >编辑</el-button
              >
              <el-button
                size="mini"
                @click="handleClick(scope.$index, scope.row)"
                :type="scope.row.status === 0 ? 'success' : ''"
                >{{ scope.row.status === 1 ? "下架" : "上架" }}</el-button
              >
              <el-button
                size="mini"
                type="danger"
                @click="handleDelete(scope.$index, scope.row)"
                >删除</el-button
              >
            </template>
          </el-table-column>
        </el-table>
      </div>
      <!-- 分页列表区域 -->
      <div class="block">
        <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="pageNum"
          :page-sizes="[20, 40, 80, 120]"
          :page-size="pageSize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total"
        >
        </el-pagination>
      </div>
    </el-card>
    <el-dialog title="新增图文" :visible.sync="dialogTableVisible" fullscreen>
      <el-form
        :model="ruleForm"
        :rules="rules"
        ref="ruleForm"
        label-width="100px"
        class="demo-ruleForm"
      >
        <el-form-item label="标题" prop="title">
          <el-input v-model="ruleForm.title" style="width: 300px"></el-input>
        </el-form-item>
        <el-form-item label="上传">
          <upLoad></upLoad>
        </el-form-item>
        <el-form-item label="试看内容" prop="tryLook">
          <div style="width: 700px">
            <editText v-model='ruleForm.tryLook'></editText>
          </div>
        </el-form-item>
        <el-form-item label="课程内容" prop="classText">
          <div style="width: 700px">
            <editText v-model='ruleForm.classText'></editText>
          </div>
        </el-form-item>
        <el-form-item label="课程价格">
          <el-input-number
            v-model="ruleForm.num"
            :min="1"
            label="描述文字"
          ></el-input-number>
        </el-form-item>
        <el-form-item label="状态">
          <el-radio v-model="ruleForm.radio" label="1">上架</el-radio>
          <el-radio v-model="ruleForm.radio" label="0">下架</el-radio>
        </el-form-item>
        <el-form-item style="float:right;padding:25px 0;">
          <el-button>取消</el-button>  
          <el-button type="primary" @click="onSumit">提交</el-button>
        </el-form-item>
      </el-form>
    </el-dialog>
  </div>
</template>
<script>
import { fetchList, updateMedia, deleteMedia } from "../../api/media";
import upLoad from "../components/upLoad";
import editText from "../../components/Tinymce/index";
export default {
  data() {
    return {
      options: [
        {
          value: 1,
          label: "已上架",
        },
        {
          value: 0,
          label: "已下架",
        },
      ],
      input: "",
      value: "",
      tableData: [],
      pageNum: 1, //当前是第几页
      pageSize: 20, // 每页20数据
      total: 0,
      dialogTableVisible: false,
      ruleForm: {
        title: "",
        num: 1,
        radio: "1",
        tryLook:'',
        classText:''
      },
      rules: {
        title: [
          { required: true, message: "请输入标题", trigger: "blur" },
          { min: 3, max: 5, message: "长度在 3 到 5 个字符", trigger: "blur" },
        ],
        tryLook: [
          { required: true, message: "请输入试看内容", trigger: "blur" },
        ],
        classText: [
          { required: true, message: "请输入课程内容", trigger: "blur" },
        ],
      },
    };
  },
  components: {
    upLoad,
    editText,
  },
  methods: {
    async getList(query) {
      let res = await fetchList(query);
      this.tableData = res.data.items;
      this.total = res.data.total;
      console.log(res)
    },
    handleSizeChange(val) {
      this.pageSize = val;
      this.getList({ page: this.pageNum, limit: val });
    },
    handleCurrentChange(val) {
      console.log(`当前页: ${val}`);
      this.pageNum = val;
      this.getList({ page: val, limit: this.pageSize });
    },
    handleEdit(index, row) {
      console.dir(row);
    },
    async handleClick(index, row) {
      let res = await updateMedia({ id: row.id });
      if (res.data === "success") {
        this.$message({
          message: "数据更新成功",
          type: "success",
        });
      }
      this.getList();
    },
    async handleDelete(index, row) {
      this.$confirm("此操作将永久删除该条数据, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
        .then(async () => {
          let res = await deleteMedia({ id: row.id });
          if (res.data === "success") {
            this.$message({
              type: "success",
              message: "删除成功!",
            });
          } else {
            this.$message({
              type: "info",
              message: "删除失败!",
            });
          }
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消删除",
          });
        });
    },
    addMedail() {
      this.dialogTableVisible = true;
    },
    onSumit(){
        console.dir(this.ruleForm)
    }
  },
  mounted() {
    this.getList();
    console.log(this.tableData);
  },
};
</script>
<style scoped>
.right {
  float: right;
  padding: 3px 0;
}
.select {
  width: 90px;
  margin-right: 15px;
}
.input {
  width: 180px;
}
.text {
  font-size: 14px;
}

.item {
  margin-bottom: 18px;
}

.clearfix:before,
.clearfix:after {
  display: table;
  content: "";
}
.clearfix:after {
  clear: both;
}

.box-card {
  width: 100%;
}
.coverText {
  display: flex;
}
.coverText img {
  width: 100px;
  height: 50px;
}
.priceText {
  display: flex;
  flex-direction: column;
  margin-left: 15px;
}
.price {
  color: red;
}
</style>