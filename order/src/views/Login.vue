<template>
  <div id="login">
    <van-nav-bar title="登录" leftText="返回" @click-left="back" leftArrow fixed>
    </van-nav-bar>
    <logo style="margin-top:60px"></logo>
    <van-cell-group>
      <van-field v-model="username" icon="clear" placeholder="请输入手机号" @click-icon="username = ''">
      </van-field>
      <van-field v-model="password" type="password" placeholder="请输入密码">
      </van-field>
    </van-cell-group>
    <p v-show="error" class="error">{{errorMsg}}</p>
    <t-button @click.native="login">登录</t-button>
    <p @click="toRegister" class="help">注册</p>
    <p>
      <van-tag plain type="primary" @click.native="login({username:'G1', password:'123'})">测试工程师1登录</van-tag>
      <van-tag plain type="success"  @click.native="login({username:'K1', password:'123'})">测试客户1登录</van-tag>
    </p>
  </div>
</template>

<script>
import TButton from '@/components/button/TButton'
import Cookies from 'js-cookie'
import Logo from '@/components/logo/Logo'
export default {
  name: 'login',
  components: {
    TButton,
    Logo
  },
  data() {
    return {
      username: '',
      password: '',
      error: false,
      errorMsg: ''
    }
  },
  methods: {
    toRegister() {
      this.$router.push('/register')
    },
    back() {
      this.$router.go(-1)
    },
    login(params) {
      // const toast = Toast.loading({
      //   mask: true,
      //   duration: 0, // 持续展示 toast
      //   forbidClick: true, // 禁用背景点击
      //   message: '登录中...'
      // })

      var data
      if (params) {
        data = params
      } else {
        if (this.username === '' || this.password === '') {
          this.error = true
          this.errorMsg = '用户名或密码不能为空'
          return
        }
        data = {
          username: this.username,
          password: this.password
        }
      }

      this.$api.LOGIN(data).then(res => {
        if (res.code === 0) {
          this.$api.GET_INFO().then(res2 => {
            if (res2.code === 0) {
              Cookies.set('_userId', {
                _id: res.data._id
              })
              this.$store.dispatch('getUserInfo', res2.data)
              this.$store.dispatch('getOrderList')
              this.$router.push('/index')
            } else if (res2.code === 1) {
              Cookies.remove('_userId')
            }
          })
        } else {
          this.error = true
          this.errorMsg = res.msg
        }
      })
    }
  },
  activated() {
    // this.$store.dispatch('getMsgList')
    // this.$store.dispatch('recvMsg')
  }
}
</script>
