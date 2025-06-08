<template>
  <div 
    v-if="visible"
    :class="[
      'position-fixed d-flex align-items-center p-3 rounded shadow-lg transition',
      type === 'success' ? 'bg-success text-white' : 'bg-danger text-white',
      positionClass
    ]"
    style="max-width: 24rem; z-index: 1050; transition: all 0.3s ease;"
  >
    <div class="me-3">
      <CheckCircleIcon v-if="type === 'success'" class="w-5 h-5" />
      <AlertCircleIcon v-else class="w-5 h-5" />
    </div>
    <div class="flex-grow-1 me-2">
      <p class="small fw-medium mb-0">{{ message }}</p>
    </div>
    <button 
      @click="close"
      class="text-white border-0 bg-transparent"
      style="outline: none;"
    >
      <XIcon class="w-4 h-4" />
    </button>
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount, computed } from 'vue'
import { CheckCircleIcon, AlertCircleIcon, XIcon } from 'lucide-vue-next'

const props = defineProps({
  message: {
    type: String,
    required: true
  },
  type: {
    type: String,
    default: 'success',
    validator: (value) => ['success', 'error'].includes(value)
  },
  duration: {
    type: Number,
    default: 3000 // 3 seconds
  },
  position: {
    type: String,
    default: 'bottom-4 right-4'
  }
})

const visible = ref(false)
let timeoutId = null

const positionClass = computed(() => {
  if (props.position === 'bottom-4 right-4') {
    return 'bottom-0 end-0 mb-4 me-4';
  }
  return props.position;
})

const close = () => {
  visible.value = false
  if (timeoutId) {
    clearTimeout(timeoutId)
  }
}

onMounted(() => {
  visible.value = true
  
  if (props.duration > 0) {
    timeoutId = setTimeout(() => {
      visible.value = false
    }, props.duration)
  }
})

onBeforeUnmount(() => {
  if (timeoutId) {
    clearTimeout(timeoutId)
  }
})
</script>
