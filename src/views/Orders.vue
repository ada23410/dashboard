<!-- eslint-disable vue/multi-word-component-names -->
<template>
    <Loading :active="isLoading"></Loading>
    <table class="table mt-4">
      <thead>
          <tr>
          <th width="120">購買時間</th>
          <th>Email</th>
          <th width="120">購買款項</th>
          <th width="120">應付金額</th>
          <th width="100">是否付款</th>
          <th width="200">編輯</th>
          </tr>
      </thead>
      <tbody>
          <tr v-for="item in orders" :key="item.id">
          <td>{{ item.created_at }}</td>
          <td>{{ item.user.email }}</td>
          <td class="text-right">
              {{ item.products }}
          </td>
          <td class="text-right">
              {{ item.total }}
          </td>
          <td>
              <span class="text-success" v-if="item.is_paid">已付款</span>
              <span class="text-muted" v-else>未付款</span>
          </td>
          <td>
              <div class="btn-group">
                  <button class="btn btn-outline-primary btn-sm"  @click="openModal(false, item)">編輯</button>
                  <button class="btn btn-outline-danger btn-sm" @click="openDeleteModal(item)">刪除</button>
              </div>
          </td>
          </tr>
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
      orders: [],
      tempOrder: {},
      pagination: {},
      isLoading: false
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
    getOrders (page = 1) {
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/orders/?page=${page}`
      this.isLoading = true
      this.$http.get(api).then((res) => {
        this.isLoading = false
        // console.log(res.data)
        if (res.data.success) {
          this.orders = res.data.orders
          this.pagination = res.data.pagination
          this.has_next = res.data.has_next
          this.has_pre = res.data.has_pre
        }
      })
    },
    openModal (item) {
      const orderComponent = this.$refs.orderModal
      orderComponent.showModal()
    },
    openDeleteModal (item) {
      this.tempOrder = { ...item }
      const delComponent = this.$refs.delOrderConfirm
      delComponent.showModal()
    },
    updateOrder (item) {
      this.tempOrder = item
      const orderComponent = this.$refs.orderModal
      this.isLoading = true
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/order/${item.id}`
      this.$http.put(api, { data: this.tempOrder }).then((res) => {
        this.isLoading = false
        // console.log(res.data)
        orderComponent.hideModal()
        if (res.data.success) {
          this.getProducts()
          this.emitter.emit('push-message', {
            style: 'success',
            title: '更新成功'
          })
        } else {
          this.emitter.emit('push-message', {
            style: 'danger',
            title: '更新失敗',
            content: res.data.messages.join('、')
          })
        }
      })
    },
    confirmDelete () {
      const url = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/order/${this.tempOrder.id}`
      this.$http.delete(url).then((res) => {
        // console.log(res)
        const delComponent = this.$refs.delOrderConfirm
        delComponent.hideModal()
        this.getOrders()
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
