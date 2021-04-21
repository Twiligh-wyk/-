<template>
  <div>
    <div class="title">
      <div class="card">
        <div class="box-card">
          <div class="img">
            <img :src="this.$route.query.data.cover" alt="" />
          </div>
          <div class="text">
            <div class="top">
              <h4>{{this.$route.query.data.title}}</h4>
              <span class="stay">{{this.$route.query.data.isend === 1 ? "已完结" : "连载中"  }}</span>
            </div>
            <p>{{this.$route.query.data.content}}</p>
            <span class="tpric">￥{{this.$route.query.data.price}}</span>
            <el-button-group class="btn">
              <el-button type="warning">{{this.$route.query.data.status === 0 ? '上架' : '下架'}}</el-button>
              <el-button>{{this.$route.query.data.isend === 0 ? '设为已完结' : '设为连载中'}}</el-button>
            </el-button-group>
          </div>
        </div>
      </div>
    </div>
    <div class="main">
    <div slot="header" class="clearfix">
      <el-button type="primary" icon="el-icon-edit"  class="music">新增目录</el-button>
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
          width="80"
          header-align="center"
          align="center"
        ></el-table-column>
        <el-table-column prop="text" label="单品内容">
          <template slot-scope="scope">
            <div class="coverText">
              <img :src="scope.row.cover" alt="" />
              <div class="priceText">
                <span>{{ scope.row.title }}</span>
                <span class="price">￥{{ scope.row.price }}</span>
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
              type="danger"
              @click="handleDelete(scope.$index, scope.row)">
              删除
              </el-button>
          </template>
        </el-table-column>
        <el-table-column           
          label="Drag"
          width="110"
          header-align="center"
          align="center"
         >
          <svg-icon icon-class="drag" class="drag-handler"/>
        </el-table-column>
      </el-table>
     </div>
    </div>
  </div>
</template>
<script>
import {fetchDetailCourse} from '../../api/column'
export default {
    data(){
        return{
            tableData:[],
            value:'',
            input:'',
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
        }
    },
  created(){
      this.getList()
    console.dir(this.$route.query.data)
    console.dir(this.$route.query.data.id)
      
  },
  methods:{
     async getList(){
          let res = await fetchDetailCourse({id:this.$route.query.data.id})
          this.tableData = res.data.items
      },
     
  }
};
</script>
<style scoped>
.card {
  border: 1px solid #eeeeee;
  width: 95%;
  height: 180px;
  margin-left: 35px;
  margin-top: 20px;
}
.box-card {
  display: flex;
  margin-left: 25px;
}
.img {
  width: 200px;
  height: 100px;
  margin-top: 20px;
  margin-right: 15px;
}
.img img {
  width: 100%;
}
.text {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  flex: 2;
}
.top {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
p {
  margin-top: -10px;
  font-size: 12px;
  color: rgb(187, 187, 187);
}
.text .tpric {
  margin: 15px 0;
  color: red;
}
.top .stay {
  color: rgb(187, 187, 187);
  margin-right: 15px;
}
.btn {
  font-size: 12px;
}
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
.main{
    margin: 30px;
}
.clearfix{
    margin: 20px 0;
}
.music{
    margin-left: 15px;
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
.drag-handler{
  width: 20px;
  height: 20px;
  cursor: pointer;
}
</style>