<template>
  <div class="auth-page">
    <div class="container page">
      <div class="row">

        <div class="col-md-6 offset-md-3 col-xs-12">
          <h1 class="text-xs-center">{{ isLogin ? 'Sign in' : 'Sign up' }}</h1>
          <p class="text-xs-center">
            <!-- <a href="">Have an account?</a> -->
            <nuxt-link v-if="isLogin" to="/register">Need an account?</nuxt-link>
            <nuxt-link v-else to="/login">Have an account?</nuxt-link>
          </p>

          <ul class="error-messages">
            <template
              v-for="(messages, field) in errors"
            >
              <li
                v-for="(message, index) in messages"
                :key="index"
              >{{ field }} {{ message }}</li>
            </template>
          </ul>

          <form @submit.prevent="onSubmit">
            <fieldset v-if="!isLogin" class="form-group">
              <input v-model="user.username" class="form-control form-control-lg" type="text" placeholder="Your Name" required>
            </fieldset>
            <fieldset class="form-group">
              <input v-model="user.email" class="form-control form-control-lg" type="email" placeholder="Email" required>
            </fieldset>
            <fieldset class="form-group">
              <input v-model="user.password" class="form-control form-control-lg" type="password" placeholder="Password" required minlength="8">
            </fieldset>
            <button class="btn btn-lg btn-primary pull-xs-right">
              {{ isLogin ? 'Sign in' : 'Sign up' }}
            </button>
          </form>
        </div>

      </div>
    </div>
  </div>
</template>

<script>
import { login, register } from '@/api/user'

// 仅在客户端加载
const Cookie = process.client ? require('js-cookie') : undefined
export default {
  name: 'LoginIndex',
  middleware: 'notAuthenticated',
  computed: {
    isLogin () {
      return this.$route.name === 'login'
    }
  },
  data () {
    return {
      user: {
        username: '',
        email: 'lpzmail@163.com',
        password: '12345678'
      },
      errors: {} // 错误信息
    }
  },
  methods: {
    async onSubmit () {
      // 提交表单请求登陆
      const { user } = this
      try {
        const { data } = this.isLogin ? await login({
          user
        }) : await register({
          user
        })
        // 保存用户的登录状态 存储到容器是为了方便共享
        this.$store.commit('setUser', data.user)

        // 为了防止刷新页面数据丢失，我们需要把数据持久化 把登陆状态存到Cookie中 
        // 浏览器刷新cookie数据不会消失
        Cookie.set('user', data.user)
        this.$router.push('/')
      } catch (err) {
        console.log('请求失败', err)
        // this.errors = err.response.data.errors
      }
    }
  }
}
</script>

<style>

</style>
