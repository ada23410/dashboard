<!-- eslint-disable vue/multi-word-component-names -->
<template>
    <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
        <div class="container-fluid">
            <router-link class="navbar-brand" to="/user/cart">用戶範例</router-link>
        </div>
    </nav>
    <div class="container-fluid mt-3 position-relative">
        <ToastMessages></ToastMessages>
        <router-view></router-view>
    </div>
</template>

<script>
import emitter from '@/methods/emitter'
import ToastMessages from '@/components/ToastMessages.vue'

export default ({
  components: {
    // eslint-disable-next-line vue/no-unused-components
    ToastMessages
  },
  provide () {
    return {
      emitter
    }
  },
  created () {
  }
})
</script>
