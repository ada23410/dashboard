<!-- eslint-disable vue/multi-word-component-names -->
<template>
    <Loading :active="isLoading"></Loading>
    <table class="table mt-4">
      <thead>
          <tr>
          <th width="120">購買時間</th>
          <th width="120">Email</th>
          <th width="200">購買款項</th>
          <th width="120">應付金額</th>
          <th width="120">是否付款</th>
          <th width="100">編輯</th>
          </tr>
      </thead>
      <tbody>
          <template v-for="(item, key) in orders" :key="key">
            <tr v-if="orders.length" :class="{'text-secondary': !item.is_paid}">
              <td>{{ $filters.date(item.create_at)}}</td>
              <td>
                <span v-text="item.user.email" v-if="item.user"></span>
              </td>
              <td>
                <ul class="list-unstyled">
                  <li v-for="(product, i) in item.products" :key="i">
                    {{ product.product.title }} 數量：{{ product.qty }}
                    {{ product.product.unit }}
                  </li>
                </ul>
              </td>
              <td class="text-right">
                  {{ item.total }}
              </td>
              <td>
                <div class="form-check form-switch">
                  <input class="form-check-input" type="checkbox" :id="`paidSwitch${item.id}`"
                        v-model="item.is_paid"
                        @change="updatePaid(item)">
                  <label class="form-check-label" :for="`paidSwitch${item.id}`">
                    <span v-if="item.is_paid">已付款</span>
                    <span v-else>未付款</span>
                  </label>
                </div>
              </td>
              <td>
                  <div class="btn-group">
                      <button class="btn btn-outline-primary btn-sm"  @click="openModal(false, item)">編輯</button>
                      <button class="btn btn-outline-danger btn-sm" @click="openDeleteModal(item)">刪除</button>
                  </div>
              </td>
            </tr>
          </template>
      </tbody>
    </table>
    <Pagination :pages="pagination" @emit-pages="getOrders"></Pagination>
    <OrderModal ref="orderModal" :order="tempOrder"  @update-order="updateOrder"></OrderModal>
    <DelOrderConfirm ref="delOrderConfirm" :order="tempOrder" @confirm-delete="confirmDelete"></DelOrderConfirm>
</template>

<script>
import OrderModal from '../components/OrderModal.vue'
import DelOrderConfirm from '../components/DelModal.vue'
import Pagination from '../components/Pagination.vue'

export default {
  data () {
    return {
      orders: {},
      tempOrder: {},
      pagination: {},
      isLoading: false,
      isNew: false,
      currentPage: 1
    }
  },
  components: {
    // eslint-disable-next-line vue/no-unused-components
    OrderModal,
    Pagination,
    DelOrderConfirm
  },
  inject: ['emitter'],
  methods: {
    getOrders (currentPage = 1) {
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/orders/?page=${currentPage}`
      this.isLoading = true
      this.$http.get(api).then((res) => {
        this.isLoading = false
        console.log(res.data)
        if (res.data.success) {
          this.orders = res.data.orders
          this.pagination = res.data.pagination
          this.has_next = res.data.has_next
          this.has_pre = res.data.has_pre
        }
      })
    },
    openModal (isNew, item) {
      this.tempOrder = { ...item }
      this.isNew = false
      const orderComponent = this.$refs.orderModal
      orderComponent.showModal()
    },
    openDeleteModal (item) {
      this.tempOrder = { ...item }
      const delComponent = this.$refs.delOrderConfirm
      delComponent.showModal()
    },
    updateOrder (item) {
      const orderComponent = this.$refs.orderModal
      this.isLoading = true
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/order/${item.id}`
      const paid = {
        is_paid: item.is_paid
      }
      this.$http.put(api, { data: paid }).then((res) => {
        this.isLoading = false
        // console.log(res.data)
        orderComponent.hideModal()
        if (res.data.success) {
          this.isLoading = false
          this.getOrders(this.currentPage)
          this.$httpMessageState(res, '更新付款狀態')
        }
      })
    },
    confirmDelete () {
      const url = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/order/${this.tempOrder.id}`
      this.isLoading = true
      this.$http.delete(url).then((res) => {
        // console.log(res)
        const delComponent = this.$refs.delOrderConfirm
        delComponent.hideModal()
        this.getOrders(this.currentPage)
      })
    //   this.$emit('delete-product', this.item)
    //   this.hideModal()
    }
  },
  created () {
    this.getOrders()
  }
}
</script>
