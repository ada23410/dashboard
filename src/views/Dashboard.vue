<!-- eslint-disable vue/multi-word-component-names -->
<template>
    <Navbar></Navbar>
    <div class="container-fluid mt-3 position-relative">
      <ToastMessages></ToastMessages>
      <router-view/>
    </div>
</template>

<script>
import emitter from '@/methods/emitter'
import ToastMessages from '@/components/ToastMessages.vue'
import Navbar from '../components/Navbar.vue'

export default {
  components: {
    Navbar,
    // eslint-disable-next-line vue/no-unused-components
    ToastMessages
  },
  provide () {
    return {
      emitter
    }
  },
  created () {
    const token = document.cookie.replace(/(?:(?:^|.*;\s*)Hexuid\s*=\s*hexToken([^;]*).*$)|^.*$/, '$1')
    console.log(token)
    this.$http.defaults.headers.common.Authorization = token
    const api = `${process.env.VUE_APP_API}api/user/check`
    // console.log(api)
    this.$http.post(api, this.user).then((res) => {
      console.log(res)
      if (!res.data.success) {
        this.$router.push('login')
        emitter.emit('push-message', {
          style: 'danger',
          title: '登入失敗'
        })
      } else {
        emitter.emit('push-message', {
          style: 'success',
          title: '登入成功'
        })
      }
    })
  }
}
</script>
