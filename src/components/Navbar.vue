<!-- eslint-disable vue/multi-word-component-names -->
<template>
    <nav class="navbar navbar-expand-lg bg-body-tertiary">
    <div class="container-fluid">
        <a class="navbar-brand" href="#">Dashboard</a>
        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav" aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarNav">
            <ul class="navbar-nav">
                <li class="nav-item">
                    <router-link to="/dashboard/products" class="nav-link" :class="{ 'active' : $route.path === '/dashboard/products'}">產品</router-link>
                </li>
                <li class="nav-item">
                    <router-link to="/dashboard/orders" class="nav-link" :class="{ 'active' : $route.path === '/dashboard/orders'}">訂單</router-link>
                </li>
                <li class="nav-item">
                    <router-link to="/dashboard/coupons" class="nav-link" :class="{ 'active' : $route.path === '/dashboard/coupons'}">優惠卷</router-link>
                </li>
                <li class="nav-item">
                    <a class="nav-link" href="#" @click.prevent="logout">登出</a>
                </li>
            </ul>
            </div>
        </div>
    </nav>
</template>

<script>
export default {
  methods: {
    logout () {
      const api = `${process.env.VUE_APP_API}logout`
      // console.log(api)
      this.$http.post(api, this.user).then((res) => {
        console.log(res)
        this.$router.push('/login')
      })
    }
  }
}
</script>
