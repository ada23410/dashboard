<template>
    <div class="container">
        <div class="row mt-4">
            <div class="col-md-7">
                <table class="table align-middle">
                    <thead>
                        <tr>
                            <th scope="col">圖片</th>
                            <th scope="col">商品名稱</th>
                            <th scope="col">價格</th>
                            <th scope="col"></th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="item in products" :key="item.id">
                            <td style="width: 200px;">
                                <div style="height: 100px; background-size: cover; background-position: center;"
                                :style="{backgroundImage: `url(${item.imageUrl})`}"></div>
                            </td>
                            <td>
                                <a href="#" class="text-dark">{{ item.title }}</a>
                            </td>
                            <td>
                                <div class="h5" v-if="!item.price">{{ item.origin_price }} 元</div>
                                <div class="h6 text-decoration-line-through" v-if="item.price">原價{{ item.origin_price }} 元</div>
                                <div class="h5" v-if="item.price">現在只要{{ item.price }} 元</div>
                            </td>
                            <td>
                                <div class="btn-group btn-group-sm">
                                    <button type="button" class="btn btn-outline-secondary" @click="getProduct(item.id)">
                                        <i class="fas fas-spinner fa-spin"></i>
                                        查看更多
                                    </button>
                                    <button type="button" class="btn btn-outline-danger" @click="addCart(item.id)" :disabled="this.status.loadingItem === item.id">
                                        <i class="fas fas-spinner fa-spin"></i>
                                        <div v-if="this.status.loadingItem === item.id" class="spinner-grow text-danger spinner-grow-sm" role="status">
                                            <span class="visually-hidden">Loading...</span>
                                        </div>
                                        加到購物車
                                    </button>
                                </div>
                            </td>
                        </tr>
                    </tbody>
                </table>
            </div>
            <div class="col-md-5">
                <h5>我的購物車</h5>
                <table class="table align-middle">
                    <thead>
                        <tr>
                            <th scope="col">品名</th>
                            <th scope="col">數量</th>
                            <th scope="col">單價</th>
                            <th scope="col"></th>
                        </tr>
                    </thead>
                    <tbody>
                        <template v-if="cart.carts">
                            <tr v-for="item in cart.carts" :key="item.id">
                                <td>
                                    {{ item.product.title }}
                                    <div class="text-success" v-if="item.coupon">
                                        已套用優惠券
                                    </div>
                                </td>
                                <td>
                                    <div class="input-group input-group-sm">
                                        <input type="number" class="form-control" v-model.number="item.qty" min="1" @change="updateCart(item)" :disabled="item.id === status.loadingItem">
                                        <div class="input-group-text">/ {{ item.product.unit }}</div>
                                    </div>
                                </td>
                                <td class="text-end">
                                    <small class="text-success" v-if="cart.final_total !== cart.total">折扣價</small>
                                    {{ $filters.currency(item.final_total) }}
                                </td>
                                <td>
                                    <button type="button" class="btn btn-outline-danger" @click="removeCartItem(item.id)" :disabled="status.loadingItem === item.id">
                                        <i class="fas fas-spinner fa-spin"></i>
                                        <i class="bi bi-trash3"></i>
                                    </button>
                                </td>
                            </tr>
                        </template>
                    </tbody>
                    <tfoot>
                        <tr>
                            <td class="text-end" colspan="3">總計</td>
                            <td class="text-end">{{ $filters.currency(cart.total) }}</td>
                        </tr>
                        <tr v-if="cart.final_total !== cart.total">
                            <td class="text-end text-success" colspan="3">折扣價</td>
                            <td class="text-end text-success">{{ $filters.currency(cart.final_total) }}</td>
                        </tr>
                    </tfoot>
                </table>
                <div class="input-group mb-3 input-group-sm">
                    <input type="text" class="form-control" v-model="coupon_code" placeholder="請輸入優惠碼">
                    <div class="input-group-append">
                        <button class="btn btn-outline-secondary" type="button" @click="addCouponCode">
                            套用優惠碼
                        </button>
                    </div>
                </div>
            </div>
        </div>
        <div class="my-5 row justify-content-center">
            <Form class="col-md-12" v-slot="{ errors }"
                    @submit="createOrder">
                <div class="mb-3">
                <label for="email" class="form-label">Email</label>
                <Field id="email" name="email" type="email" class="form-control"
                        :class="{ 'is-invalid': errors['email'] }"
                        placeholder="請輸入 Email" rules="email|required"
                        v-model="form.user.email"></Field>
                <ErrorMessage name="email" class="invalid-feedback"></ErrorMessage>
                </div>

                <div class="mb-3">
                <label for="name" class="form-label">收件人姓名</label>
                <Field id="name" name="姓名" type="text" class="form-control"
                        :class="{ 'is-invalid': errors['姓名'] }"
                        placeholder="請輸入姓名" rules="required"
                        v-model="form.user.name"></Field>
                <ErrorMessage name="姓名" class="invalid-feedback"></ErrorMessage>
                </div>

                <div class="mb-3">
                <label for="tel" class="form-label">收件人電話</label>
                <Field id="tel" name="電話" type="tel" class="form-control"
                        :class="{ 'is-invalid': errors['電話'] }"
                        placeholder="請輸入電話" rules="required"
                        v-model="form.user.tel"></Field>
                <ErrorMessage name="電話" class="invalid-feedback"></ErrorMessage>
                </div>

                <div class="mb-3">
                <label for="address" class="form-label">收件人地址</label>
                <Field id="address" name="地址" type="text" class="form-control"
                        :class="{ 'is-invalid': errors['地址'] }"
                        placeholder="請輸入地址" rules="required"
                        v-model="form.user.address"></Field>
                <ErrorMessage name="地址" class="invalid-feedback"></ErrorMessage>
                </div>

                <div class="mb-3">
                <label for="message" class="form-label">留言</label>
                <textarea name="" id="message" class="form-control" cols="30" rows="10"
                            v-model="form.message"></textarea>
                </div>
                <div class="text-end">
                <button class="btn btn-danger">送出訂單</button>
                </div>
            </Form>
        </div>
    </div>
</template>

<script>
export default ({
  data () {
    return {
      products: [],
      product: {},
      cart: {},
      coupon_code: '',
      status: {
        loadingItem: '' // 對應品項id
      },
      form: {
        user: {
          name: '',
          email: '',
          tel: '',
          address: ''
        },
        message: ''
      }
    }
  },
  methods: {
    getProducts () {
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/products/all`
      this.isLoading = true
      this.$http.get(api).then((res) => {
        console.log(res.data)
        this.products = res.data.products
        this.isLoading = false
      })
    },
    getProduct (id) {
      this.$router.push(`/user/product/${id}`)
    },
    addCart (id) {
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/cart`
      this.status.loadingItem = id
      const cart = {
        product_id: id,
        qty: 1
      }
      this.$http.post(api, { data: cart }).then((res) => {
        this.status.loadingItem = ''
        console.log(res.data)
      })
    },
    getCart () {
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/cart`
      this.$http.get(api).then((res) => {
        console.log(res.data.data)
        this.cart = res.data.data
      })
    },
    updateCart (item) {
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/cart/${item.id}`
      this.isLoading = true
      this.status.loadingItem = item.id
      const cart = {
        product_id: item.product_id,
        qty: item.qty
      }
      this.$http.put(api, { data: cart }).then((res) => {
        console.log(res.data)
        this.status.loadingItem = ''
        this.getCart()
      })
    },
    addCouponCode () {
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/coupon`
      const coupon = {
        code: this.coupon_code
      }
      this.$http.post(api, { data: coupon }).then((res) => {
        console.log(res)
        this.getCart()
      })
    },
    createOrder () {
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/order`
      const order = this.form
      this.$http.post(api, { data: order }).then((res) => {
        console.log(res)
      })
    }
  },
  created () {
    this.getProducts()
    this.getCart()
  }
})
</script>
