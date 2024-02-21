<!-- 定位使用做列表呈現 -->
<template>
    <div class="toast-container position-fixed bottom-0 end-0 p-3">
        <Toast v-for="(msg, key) in messages" :key="key" :msg="msg"></Toast>
    </div>
</template>

<script>
import Toast from '@/components/Toast.vue'

export default {
  components: { Toast },
  data () {
    return {
      messages: []
    }
  },
  inject: ['emitter'],
  mounted () {
    this.emitter.on('push-message', (message) => {
      const { style = 'success', title, content } = message
      this.messages.push({ style, title, content })
    })
  }
}
</script>
