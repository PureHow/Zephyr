<template>
  <div class="z-header">
    <el-container>
      <el-header>
        <el-menu :default-active="activeMenuItem" mode="horizontal">
          <el-menu-item index="loginDialog" v-on:click="showLoginDialog">登录</el-menu-item>
        </el-menu>
      </el-header>
    </el-container>
    <el-dialog title="登录" :visible.sync="visibleLoginDialog">
      <el-form ref="loginForm" :model="loginForm" label-width="80px">
        <el-form-item label="用户名">
          <el-input type="text" v-model="loginForm.username" autofocus="autofocus"></el-input>
        </el-form-item>
        <el-form-item label="密码">
          <el-input type="password" v-model="loginForm.password"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" v-on:click="submitLogin">登录</el-button>
        </el-form-item>
      </el-form>
    </el-dialog>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator'
import axios from 'axios'
@Component
export default class Header extends Vue {
  activeMenuItem: string = 'loginDialog'
  visibleLoginDialog: boolean = false
  loginForm: any = {
    username: '',
    password: ''
  }
  showLoginDialog (): void {
    this.visibleLoginDialog = true
  }
  submitLogin (): void {
    axios.post('/hub/login', this.loginForm)
      .then(function (response) {
        // eslint-disable-next-line
        console.log(response)
      }).catch(function (error) {
        // eslint-disable-next-line
        console.log(error)
      })
  }
}
</script>

<style scoped lang="scss">
.el-menu {
  float: right;
}
</style>
