<template>
    <!-- Modal -->
    <div class="modal fade" id="exampleModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true" ref="modal">
        <div class="modal-dialog modal-xl" role="document">
            <div class="modal-content border-0">
                <div class="modal-header bg-dark text-white">
                <h5 class="modal-title" id="exampleModalLabel">
                    <span>訂單詳細資料</span>
                </h5>
                <button type="button" class="btn-close"
                        data-bs-dismiss="modal" aria-label="Close"></button>
                </div>
                <div class="modal-body">
                <div class="row">
                    <div class="col-sm-4">
                        <div class="mb-3">
                            <h5>用戶資料</h5>
                            <table class="table">
                                <tbody v-if="tempOrder.user">
                                    <tr>
                                        <th scope="col">姓名</th>
                                        <th scope="col">{{ tempOrder.user.name }}</th>
                                    </tr>
                                    <tr>
                                        <th scope="row">Email</th>
                                        <td>{{ tempOrder.user.email }}</td>
                                    </tr>
                                    <tr>
                                        <th scope="row">電話</th>
                                        <td>{{ tempOrder.user.tel }}</td>
                                    </tr>
                                    <tr>
                                        <th scope="row">地址</th>
                                        <td>{{ tempOrder.user.address }}</td>
                                    </tr>
                                </tbody>
                            </table>
                        </div>
                    </div>
                    <div class="col-sm-8">
                        <div class="mb-3">
                            <h5>訂單細節</h5>
                            <table class="table">
                                <tbody>
                                    <tr>
                                        <th scope="col">訂單編號</th>
                                        <th scope="col">{{ tempOrder.id }}</th>
                                    </tr>
                                    <tr>
                                        <th scope="row">下單時間</th>
                                        <td>{{ $filters.date(tempOrder.create_at) }}</td>
                                    </tr>
                                    <tr>
                                        <th scope="row">付款時間</th>
                                        <td>
                                            <span v-if="tempOrder.paid_date">{{ $filters.date(tempOrder.paid_date) }}</span>
                                            <span v-else>時間不正確</span>
                                        </td>
                                    </tr>
                                    <tr>
                                        <th scope="row">付款狀態</th>
                                        <td>
                                            <strong v-if="tempOrder.is_paid" class="text-success">已付款</strong>
                                            <span v-else class="text-muted">尚未付款</span>
                                        </td>
                                    </tr>
                                    <tr>
                                        <th scope="row">總金額</th>
                                        <td>{{ $filters.currency(tempOrder.total) }}</td>
                                    </tr>
                                </tbody>
                            </table>
                        </div>
                        <div class="mb-3">
                            <h5>選購商品</h5>
                            <table class="table">
                                <thead>
                                    <tr></tr>
                                    <tr></tr>
                                </thead>
                                <tbody>
                                    <tr v-for="item in tempOrder.products" :key="item.id">
                                        <th scope="col">{{ item.product.title }}</th>
                                        <th scope="col">{{ item.qty }} / {{ item.product.unit }}</th>
                                        <th scope="col">{{ $filters.currency(item.final_total) }}</th>
                                    </tr>
                                </tbody>
                            </table>
                        </div>
                    </div>
                </div>
                <div class="modal-footer">
                <button type="button" class="btn btn-outline-secondary"
                        data-bs-dismiss="modal">取消
                </button>
                <button type="button" class="btn btn-primary" @click="$emit('update-order', tempOrder)">確認</button>
                </div>
            </div>
        </div>
        </div>
    </div>
</template>

<script>
import Modal from 'bootstrap/js/dist/modal'

export default ({
  props: {
    order: {
      type: Object,
      default: () => ({})
    }
  },
  watch: {
    // 監聽內層的props
    order () {
      // 無法直接修改外層數據，故儲存product到內層的temProduct
      this.tempOrder = this.order
      this.isPaid = this.tempOrder.is_paid
    }
  },
  data () {
    return {
      modal: '',
      tempOrder: {},
      status: {},
      isPaid: false
    }
  },
  emits: ['update-product'],
  // eslint-disable-next-line no-undef
  inject: ['emitter'],
  methods: {
    showModal () {
      this.modal.show()
    },
    hideModal () {
      this.modal.hide()
    }
  },
  mounted () {
    // eslint-disable-next-line no-undef
    this.modal = new Modal(this.$refs.modal)
  }
})
</script>
