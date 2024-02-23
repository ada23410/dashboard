<template>
    <!-- Modal -->
    <div class="modal fade" id="delModal" tabindex="-1" aria-labelledby="delModalLabel" aria-hidden="true" ref="modal">
        <div class="modal-dialog">
        <div class="modal-content">
            <div class="modal-header">
            <h5 class="modal-title" id="delModalLabel">刪除確認</h5>
            <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
            </div>
            <div class="modal-body">
                您確定要刪除這個商品？
            </div>
            <div class="modal-footer">
            <button type="button" class="btn btn-secondary" @click="hideModal">取消</button>
            <button type="button" class="btn btn-danger" @click="$emit('confirm-delete')">删除</button>
            </div>
        </div>
        </div>
    </div>
</template>

<script>
// 內層
// 引入boostrap的方法
import modalMixin from '@/mixins/modalMixin'
// 在此元件新增方法，讓外部呼叫此方法
export default ({
  props: {
    item: {}
  },
  data () {
    return {
      modal: ''
    }
  },
  mixins: [modalMixin]
})
</script>
