<!-- eslint-disable vue/multi-word-component-names -->
<template>
    <Loading :active="isLoading"></Loading>
    <div class="text-end">
      <button class="btn btn-primary" type="button" @click="openModal(true)">新增優惠券</button>
    </div>
    <table class="table mt-4">
      <thead>
          <tr>
          <th width="120">名稱</th>
          <th width="120">折扣百分比</th>
          <th width="120">到期日</th>
          <th width="120">是否啟用</th>
          <th width="120">編輯</th>
          </tr>
      </thead>
      <tbody>
          <tr v-for="(item, key ) in coupons" :key="key">
          <td>{{ item.title }}</td>
          <td>{{ item.percent }}</td>
          <td class="text-right">
              {{ $filters.date(item.due_date) }}
          </td>
          <td>
              <span class="text-success" v-if="item.is_enabled === 1">啟用</span>
              <span class="text-muted" v-else>未啟用</span>
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
    <CouponModal ref="couponModal" :coupon="tempCoupon" @update-coupon="updateCoupon"></CouponModal>
    <DelCouponConfirm ref="delCouponConfirm" :coupon="tempCoupon" @confirm-delete="confirmDelete"></DelCouponConfirm>
</template>

<script>
import CouponModal from '../components/CouponModal.vue'
import DelCouponConfirm from '../components/DelModal.vue'

export default ({
  data () {
    return {
      coupons: [],
      pagination: {},
      tempCoupon: {
        title: '',
        is_enabled: 0,
        percent: 100,
        code: ''
      },
      isNew: false,
      isLoading: false
    }
  },
  components: {
    CouponModal,
    // eslint-disable-next-line vue/no-unused-components
    DelCouponConfirm
  },
  props: {
    config: Object
  },
  inject: ['emitter'],
  methods: {
    openModal (isNew, item) {
      // console.log(isNew, item)
      if (isNew) {
        this.tempCoupon = {
          due_date: new Date().getTime() / 1000
        }
      } else {
        this.tempCoupon = { ...item }
      }
      this.isNew = isNew
      const couponComponent = this.$refs.couponModal
      couponComponent.showModal()
    },
    openDeleteModal (item) {
      this.tempCoupon = { ...item }
      const delComponent = this.$refs.delCouponConfirm
      delComponent.showModal()
    },
    getCoupons () {
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/coupons`
      this.$http.get(api).then((res) => {
        console.log(res.data)
        // eslint-disable-next-line no-empty
        if (res.data.success) {
          this.coupons = res.data.coupons
          this.pagenation = res.data.pagination
        }
      })
    },
    updateCoupon (item) {
      this.tempCoupon = item
      // 新增
      let api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/coupon`
      let httpMethod = 'post'

      // 編輯
      if (!this.isNew) {
        api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/coupon/${item.id}`
        // eslint-disable-next-line no-unused-vars
        httpMethod = 'put'
      }

      const couponComponent = this.$refs.couponModal
      this.isLoading = true
      this.$http[httpMethod](api, { data: this.tempCoupon }).then((res) => {
        console.log(res.data)
        this.isLoading = false
        couponComponent.hideModal()
        if (res.data.success) {
          this.$httpMessageState(res, '新增優惠券')
          this.getCoupons()
        } else {
          this.emitter.emit('push-message', {
            style: 'danger',
            title: '更新失敗'
            // content: res.data.messages.join('、')
          })
        }
      })
    },
    confirmDelete () {
      const url = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/coupon/${this.tempCoupon.id}`
      this.isLoading = true
      this.$http.delete(url).then((res) => {
        console.log(res)
        this.isLoading = false
        this.$httpMessageState(res, '刪除優惠券')
        const delComponent = this.$refs.delCouponConfirm
        delComponent.hideModal()
        this.getCoupons()
      })
    }
  },
  created () {
    this.getCoupons()
  }
})
</script>
