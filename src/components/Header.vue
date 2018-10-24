<template>
  <div class="z-header">
    <el-container>
      <el-header>
        <el-menu :default-active="activeMenuItem" mode="horizontal">
          <el-submenu v-if="loginStatus" index="submenu-profile">
            <template slot="title">Profile</template>
            <el-menu-item index="profile">个人中心</el-menu-item>
            <el-menu-item index="exit" v-on:click="logout">退出</el-menu-item>
          </el-submenu>
          <el-menu-item v-else index="loginDialog" v-on:click="showLoginDialog">登录</el-menu-item>
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
  loginStatus: boolean = false
  loginForm: any = {
    username: '',
    password: ''
  }
  cleanLoginForm (): void {
    this.loginForm.username = ''
    this.loginForm.password = ''
  }
  showLoginDialog (): void {
    this.visibleLoginDialog = true
  }
  showTip (message: string, type: string = 'success'): void {
    switch (type) {
      case 'success':
        Vue.prototype.$message({
          message: message,
          type: type
        })
        break
      case 'error':
        Vue.prototype.$message.error(message)
        break
      default:
        Vue.prototype.$message(message)
    }
  }
  submitLogin (): void {
    var self = this
    axios.post('/hub/login', this.loginForm)
      .then(function (response) {
        if (response.data.code === 0) { // OK
          self.cleanLoginForm()
          self.visibleLoginDialog = flase
          self.showTip('登录成功', 'success')
        } else if (response.data.code === 100000) { // Unknow Error
          self.showTip('登录失败：未知错误', 'error')
        } else if (response.data.code === 200001) { // Login Failed
          self.showTip('登录失败：用户名或密码错误', 'error')
        }
        self.loginStatus = true
      }).catch(function (error) {
        // eslint-disable-next-line
        console.log(error)
        self.loginStatus = false
      })
  }
  logout (): void {
    var self = this
    axios.post('/hub/logout', {})
      .then(function (response) {
        self.loginStatus = false
        self.showTip('已退出登录', 'success')
      }).catch(function (error) {
        self.showTip('退出失败', 'error')
      })
  }
  beforeMount (): void {
    var self = this
    axios.get('/hub/session')
      .then(function (response) {
        // eslint-disable-next-line
        console.log(response.status)
        self.loginStatus = true
      }).catch(function (error) {
        // eslint-disable-next-line
        console.log(error.response.status)
        self.loginStatus = false
      })
  }
}
</script>

<style scoped lang="scss">
.el-menu {
  float: right;
}
</style>
