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
                                <thead>
                                    <tr>
                                    <th scope="col">姓名</th>
                                    <th scope="col">aaa</th>
                                    </tr>
                                </thead>
                                <tbody>
                                    <tr>
                                    <th scope="row">Email</th>
                                    <td>aaa@aaa.aaa</td>
                                    </tr>
                                    <tr>
                                    <th scope="row">電話</th>
                                    <td>1234-5678</td>
                                    </tr>
                                    <tr>
                                    <th scope="row">地址</th>
                                    <td>1234-5678</td>
                                    </tr>
                                </tbody>
                            </table>
                        </div>
                    </div>
                    <div class="col-sm-8">
                        <div class="mb-3">
                            <h5>訂單細節</h5>
                            <table class="table">
                                <thead>
                                    <tr>
                                        <th scope="col">訂單編號</th>
                                        <th scope="col">12345678910</th>
                                    </tr>
                                </thead>
                                <tbody>
                                    <tr>
                                        <th scope="row">下單時間</th>
                                        <td>2024/02/23</td>
                                    </tr>
                                    <tr>
                                        <th scope="row">付款時間</th>
                                        <td>2024/02/23</td>
                                    </tr>
                                    <tr>
                                        <th scope="row">付款狀態</th>
                                        <td>已付款</td>
                                    </tr>
                                    <tr>
                                        <th scope="row">總金額</th>
                                        <td>13,600</td>
                                    </tr>
                                </tbody>
                            </table>
                        </div>
                        <div class="mb-3">
                            <h5>選購商品</h5>
                            <table class="table">
                                <thead>
                                    <tr>
                                        <th scope="col">好看的外套</th>
                                        <th scope="col">1 / 件</th>
                                        <th scope="col">950</th>
                                    </tr>
                                </thead>
                                <tbody>
                                    <tr>
                                        <th scope="col">好看的外套</th>
                                        <th scope="col">1 / 件</th>
                                        <th scope="col">950</th>
                                    </tr>
                                    <tr>
                                        <th scope="col">好看的外套</th>
                                        <th scope="col">1 / 件</th>
                                        <th scope="col">950</th>
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
                <button type="button" class="btn btn-primary">確認</button>
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
    }
  },
  data () {
    return {
      modal: {},
      tempOrder: {}
    }
  },
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
