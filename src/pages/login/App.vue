<!--
 * @Author: linzq
 * @Date: 2020-11-25 14:32:29
 * @LastEditors: linzq
 * @LastEditTime: 2021-02-20 14:29:07
 * @Description: 登录页
-->
<template>
  <div id="login">
    <el-form label-width="80px" :model="form">
      <el-form-item label="用户名">
        <el-input v-model="form.name"></el-input>
      </el-form-item>
      <el-form-item label="密码">
        <el-input type="password" v-model="form.password"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="signIn">登录</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
import { JSEncrypt } from 'jsencrypt'
import { rsaEncrypt } from '@/utils/index.js'

export default {
  name: 'Login',
  data() {
    return {
      form: {
        name: '',
        password: ''
      }
    }
  },
  methods: {
    signIn() {
      window.location.href = 'main.html' // 跳过登录
      // this.publicKey()
    },
    async publicKey() {
      // 获取加密公钥
      const res = await this.$api.login.publicKey()
      if (res) {
        this.$wsCache.set('publicKey', res)
        const encrypt = new JSEncrypt()
        encrypt.setPublicKey(res)
        const encrypted = encrypt.encrypt(this.form.password)
        this.$set(this.form, 'password', encrypted)
        this.login()
      }
    },
    async login() {
      //  加密后登录
      const res = await this.$api.login.login(this.form)
      if (res) {
        this.$wsCache.set('userInfo', res)
        window.location.href = 'main.html'
      }
    }
  }
}
</script>

<style>
#login {
  margin: 20% 35%;
  text-align: center;
  width: 30%;
}
</style>
