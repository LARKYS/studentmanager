<template>
  <div class="login-page">
    <el-form ref="AccountFrom" :model="account" :rules="rules" lable-position="left" lable-with="0px"
             class="demo-ruleFrom login-container">
      <h1 class="title" style="color: gold;">管理系统</h1>
      <el-form-item prop="username">
        <el-input type="text" v-model="account.username" size="small" auto-complete="off" placeholder="账号"></el-input>
      </el-form-item>
      <el-form-item prop="pwd">
        <el-input type="password" v-model="account.pwd" auto-complete="off" placeholder="密码"></el-input>
      </el-form-item>
      <el-checkbox v-model="checked" checked class="remember">记住密码</el-checkbox>
      <el-form-item style="width: 100%;">
        <el-button type="primary" style="width: 100%;" @click.native.prevent="handleLogin" :loading="logining">登陆</el-button>
      </el-form-item>
    </el-form>
  </div>

</template>

<script>
  import {requestLogin} from "../api/api";
  export default {
    name: "login",
    data(){
      return {
        logining: false,
        account:{
          username: '',
          pwd: ''
        },
        rules: {
          username:[{
            required:true,
            message: '请输入账号',
            trigger: 'blur'
          }],
          pwd: [{
            required: true,
            message: '请输入密码',
            trigger: 'blur'
          }]
        },
        checked: true
      }
    },
    methods: {
      handleLogin(){
        this.$refs.AccountFrom.validate((valid)=>{
          if (valid){
            //通过验证
            this.logining = true;
            var loginParams = {cUsername: this.account.username,cPwd:this.account.pwd};
            //调用函数，传参
            requestLogin(loginParams).then(data=>{
              this.logining = false;
              if (data.code == 200){
                //登陆成功
                sessionStorage.setItem('access-token',data.token);
                //用 vue 路由跳转到后端
                this.$router.push({path: '/home'});
              }else {
                this.message({
                  message: data.msg,
                  type: 'error'
                })
              }
            })
          }else {
            console.log('error summit')
            return false;
          }
        });
      }
    }
  }
</script>

<style>
  body{
    background: #DFE9FB;
  }
  .login-page {
    position: fixed;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    background-image: url("../assets/images/login-bg.png");
    background-repeat: no-repeat;
    background-size: cover;
    overflow: hidden;
  }
  .login-container{
    position: fixed;
    top: 150px;
    width: 350px;
    margin-left: 35%;
  }
</style>
