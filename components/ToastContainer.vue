<template>
  <div class="toast-container">
    <TransitionGroup name="toast">
      <ToastNotification
        v-for="toast in toasts"
        :key="toast.id"
        :message="toast.message"
        :type="toast.type"
        :duration="toast.duration"
        :position="toast.position"
        @close="removeToast(toast.id)"
      />
    </TransitionGroup>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import ToastNotification from './ToastNotification.vue'

const toasts = ref([])
let nextId = 0

const addToast = (toast) => {
  const id = nextId++
  toasts.value.push({
    id,
    ...toast
  })
  
  if (toast.duration !== 0) {
    setTimeout(() => {
      removeToast(id)
    }, toast.duration || 3000)
  }
  
  return id
}

const removeToast = (id) => {
  const index = toasts.value.findIndex(toast => toast.id === id)
  if (index !== -1) {
    toasts.value.splice(index, 1)
  }
}

// Expose methods to parent components
defineExpose({
  addToast,
  removeToast
})
</script>

<style scoped>
.toast-enter-active,
.toast-leave-active {
  transition: all 0.3s ease;
}

.toast-enter-from {
  transform: translateY(100%);
  opacity: 0;
}

.toast-leave-to {
  transform: translateY(-100%);
  opacity: 0;
}
</style>
