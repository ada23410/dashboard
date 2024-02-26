<template>
    <div class="container">
        <nav aria-label="breadcrumb">
            <ol class="breadcrumb">
                <li class="breadcrumb-item"><router-link to="/user/cart">商品列表</router-link></li>
                <li class="breadcrumb-item active" aria-current="page">{{ product.title }}</li>
            </ol>
        </nav>
        <div class="row">
            <div class="col-md-8">
                <div class="h5">{{ product.title }}</div>
                <div>{{ product.content }}</div>
                <div>{{ product.description }}</div>
                <img :src="product.imageUrl" alt="" class="img-fluid mb-3">
            </div>
            <div class="col-md-4">
                <div class="h5" v-if="!product.price">{{ product.origin_price }}元</div>
                <div class="h6" v-if="product.price">原價{{ product.origin_price }}元</div>
                <div class="h5" v-if="product.price">現在只要{{ product.price }}元</div>
                <hr>
                <button type="button" class="btn btn-outline-danger" @click="addCart(product.id)" :disabled="this.status.loadingItem === product.id">
                    <i class="fas fas-spinner fa-spin"></i>
                    <div v-if="this.status.loadingItem === product.id" class="spinner-grow text-danger spinner-grow-sm" role="status">
                        <span class="visually-hidden">Loading...</span>
                    </div>
                    加到購物車
                </button>
            </div>
        </div>
    </div>
</template>

<script>
export default ({
  data () {
    return {
      product: {},
      status: {
        loadingItem: '' // 對應品項id
      }
    }
  },
  methods: {
    getProduct (productId) {
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/product/${productId}`
      // console.log(api)
      this.$http.get(api).then((res) => {
        // console.log(res.data)
        this.product = res.data.product
      })
    },
    addCart (productId) {
      // console.log(productId)
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/cart`
      this.status.loadingItem = productId
      const cart = {
        product_id: productId,
        qty: 1
      }
      this.$http.post(api, { data: cart }).then((res) => {
        this.status.loadingItem = ''
        console.log(res.data)
      })
    }
  },
  created () {
    const productId = this.$route.params.productId
    this.getProduct(productId)
  }
})
</script>
