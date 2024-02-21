<!-- Toast元件的原型 -->
<!-- eslint-disable vue/multi-word-component-names -->
<template>
    <div class="toast" role="alert" aria-live="assertive" aria-atomic="true" ref="toast">
        <div class="toast-header">
            <span :class="`bg-${msg.style}`" class="p-2 rounded me-2 d-inline-block"></span>
            <strong class="me-auto">{{ msg.title }}</strong>
            <small class="text-body-secondary">11 mins ago</small>
            <button type="button" class="btn-close" data-bs-dismiss="toast" aria-label="Close"></button>
        </div>
        <div class="toast-body" v-if="msg.content">
            {{ msg.content }}
        </div>
    </div>
</template>

<script>
// 載入boostrap toast元件
import Toast from 'bootstrap/js/dist/toast'

export default {
  // eslint-disable-next-line vue/multi-word-component-names
  name: 'Toast',
  props: [
    'msg'
  ],
  mounted () {
    const toastEl = this.$refs.toast
    const toast = new Toast(toastEl, {
      delay: 5000
    })
    toast.show()
  }
}
</script>
