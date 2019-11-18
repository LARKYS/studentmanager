<template>
  <el-container class="el-container">
    <el-header class="el-header">Header</el-header>
    <el-container>
      <el-aside width="200px" style="background-color: rgb(238, 241, 246)">
        <el-menu :default-openeds="['1', '3']">
          <el-submenu index="1">
            <template slot="title"><i class="el-icon-message"></i>导航一</template>
            <el-menu-item-group>
              <template slot="title">分组一</template>
              <el-menu-item index="1-1">选项1</el-menu-item>
              <el-menu-item index="1-2">选项2</el-menu-item>
            </el-menu-item-group>
            <el-menu-item-group title="分组2">
              <el-menu-item index="1-3">选项3</el-menu-item>
            </el-menu-item-group>
            <el-submenu index="1-4">
              <template slot="title">选项4</template>
              <el-menu-item index="1-4-1">选项4-1</el-menu-item>
            </el-submenu>
          </el-submenu>
          <el-submenu index="2">
            <template slot="title"><i class="el-icon-menu"></i>导航二</template>
            <el-menu-item-group>
              <template slot="title">分组一</template>
              <el-menu-item index="2-1">选项1</el-menu-item>
              <el-menu-item index="2-2">选项2</el-menu-item>
            </el-menu-item-group>
            <el-menu-item-group title="分组2">
              <el-menu-item index="2-3">选项3</el-menu-item>
            </el-menu-item-group>
            <el-submenu index="2-4">
              <template slot="title">选项4</template>
              <el-menu-item index="2-4-1">选项4-1</el-menu-item>
            </el-submenu>
          </el-submenu>
          <el-submenu index="3">
            <template slot="title"><i class="el-icon-setting"></i>导航三</template>
            <el-menu-item-group>
              <template slot="title">分组一</template>
              <el-menu-item index="3-1">选项1</el-menu-item>
              <el-menu-item index="3-2">选项2</el-menu-item>
            </el-menu-item-group>
            <el-menu-item-group title="分组2">
              <el-menu-item index="3-3">选项3</el-menu-item>
            </el-menu-item-group>
            <el-submenu index="3-4">
              <template slot="title">选项4</template>
              <el-menu-item index="3-4-1">选项4-1</el-menu-item>
            </el-submenu>
          </el-submenu>
        </el-menu>
      </el-aside>
      <el-container>
        <el-main>
          <div>
            <el-col :span="24" class="toolbar" style="padding-bottom:0px">
              <el-form :inline="true" :model="filters">
                <el-form-item>
                  <el-input v-model="filters.name" placeholder="姓名"></el-input>
                </el-form-item>
                <el-form-item>
                  <el-button type="primary" v-on:click="getUsers">查询</el-button>
                </el-form-item>
                <el-form-item>
                  <el-button type="info" @click="addUser">新增</el-button>
                </el-form-item>
              </el-form>
            </el-col>
            <el-table :data="userInfoList" style="width: 100%;">
              <el-table-column prop="cId" label="id" width="180"></el-table-column>
              <el-table-column prop="cUsername" label="名字" width="180"></el-table-column>
              <el-table-column prop="cPwd" label="密码" width="180"></el-table-column>
              <el-table-column label="操作" align="center" min-width="100">
                <template slot-scope="scope">
                  <el-button type="text" @click="checkDetail(scope.row)">查看详情</el-button>
                  <el-button type="info" @click="modifyUser(scope.row)">修改</el-button>
                  <el-button type="info" @click="deleteUser(scope.row)">删除</el-button>
                </template>
              </el-table-column>
            </el-table>
            <!--新增界面-->
            <el-dialog title="记录" :visible.sync="dialogVisible" width="50%" :close-on-click-modal="false">
              <el-form :model="addFormData" :rules="rules2" ref="addFormData" label-width="0px"
                       class="demo-ruleForm login-container">
                <el-form-item prop="cUsername">
                  <el-input type="text" v-model="addFormData.cUsername" auto-complete="off" placeholder="账号"></el-input>
                </el-form-item>
                <el-form-item prop="cUsername">
                  <el-input type="password" v-model="addFormData.cPwd" auto-complete="off" placeholder="密码"></el-input>
                </el-form-item>
              </el-form>
              <span slot="footer" class="dialog-footer">
        <el-button @click.native="dialogVisible = false,addFormData={cId:'',cUsername:'',cPwd:''}">取消</el-button>
        <el-button v-if="isView" type="primary" @click.native="addSubmit">确定</el-button>
      </span>
            </el-dialog>
          </div>
        </el-main>
        <el-footer>Footer</el-footer>
      </el-container>
    </el-container>
  </el-container>

</template>

<script>
  import {userList} from '../api/api';
  import axios from 'axios';
  import qs from 'qs';

  export default {
    name: "home",
    data() {
      return {
        userInfoList: [],
        addFormReadOnly: true,
        dialogVisible: false,
        isView: true,
        addFormData: {
          cId: '',
          cUsername: '',
          cPwd: ''
        },
        rules2: {
          cUsername: [{
            required: true,
            message: '用户名不能为空',
            trigger: 'blur'
          }],
          cPwd: [{
            required: true,
            message: '密码不能为空',
            trigger: 'blur'
          }]
        },
        filters:{
          name:''
        }
      }
    },
    mounted:function () {
      this.loadData();
    },
    methods:{
      loadData(){
        let param = {filter:this.filters.name};
        axios.post('/user/userlist',qs.stringify(param)).then((result)=>{
          var _data = result.data;
          this.userInfoList = _data;
        });

      },
      getUsers(){
        this.loadData();
      },
      addUser(){
        this.addFormData = {
          cId: '',
          cUsername: '',
          cPwd: ''
        };
        this.isView = true;
        this.dialogVisible = true;
      },
      modifyUser(rowData) {
        this.addFormData = Object.assign({}, rowData);
        this.isView = true;
        this.dialogVisible = true;
        //    this.addFormReadOnly = false;
      },
      checkDetail(rowData) {
        this.addFormData = Object.assign({}, rowData);
        this.isView = false;
        this.dialogVisible = true;
        //    this.addFormReadOnly = true;
      },
      deleteUser(rowData){
        this.$alert('是否删除这条记录','信息删除',{
          confirmButtonContext: '确定',
          callback: action =>{
            var params = {
              userId: rowData.cId
            };
            axios.post('/user/delete',qs.stringify(params)).then((result)=>{
              console.log(result);
              if (result.data.success){
                this.$message({
                  type: 'info',
                  message: '已删除'
                });
              }else {
                this.$message({
                  type: 'info',
                  message: '删除失败'
                })
              }
              this.loadData();
            })
          }
        })
      },
      addSubmit(){
        this.$refs.addFormData.validate((valid)=>{
          //代表通过验证，将参数传回后台
          if (valid){
            let param = Object.assign({},this.addFormData);
            axios.post('/user/submit',qs.stringify(param)).then((result)=>{
              if (result.data.success){
                this.$message({
                  type: 'info',
                  message: '添加成功'
                })
              }else {
                this.$message({
                  type: 'info',
                  message: '添加失败'
                })
              }
              this.loadData();
              this.dialogVisible = false;
            })
          }
        });
      }

    }
  }
</script>

<style scoped>
  .el-header, .el-footer {
    background-color: #24292e;
    color: hsla(0,0%,100%,.7);
    text-align: center;
    line-height: 60px;
  }

  .el-aside {
    background-color: #D3DCE6;
    color: #333;
    text-align: center;
    line-height: 200px;
  }

  .el-main {
    background-color: #E9EEF3;
    color: #333;
    text-align: center;
    line-height: 10px;
    padding: 0px;
  }

  body > .el-container {
    position: fixed;
    margin-top: 0px;
    margin-bottom: 40px;
  }

  .el-container:nth-child(5) .el-aside, .el-container:nth-child(6) .el-aside {
    line-height: 260px;
  }

  .el-container:nth-child(7) .el-aside {
    line-height: 320px;
  }
</style>
