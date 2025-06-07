<template>
  <div 
    v-if="visible"
    :class="[
      'fixed z-50 flex items-center p-4 rounded-md shadow-lg transition-all duration-300 transform',
      type === 'success' ? 'bg-green-500 text-white' : 'bg-red-500 text-white',
      position
    ]"
    style="max-width: 24rem;"
  >
    <div class="flex-shrink-0 mr-3">
      <CheckCircleIcon v-if="type === 'success'" class="w-5 h-5" />
      <AlertCircleIcon v-else class="w-5 h-5" />
    </div>
    <div class="flex-1 mr-2">
      <p class="text-sm font-medium">{{ message }}</p>
    </div>
    <button 
      @click="close"
      class="flex-shrink-0 text-white hover:text-gray-200 focus:outline-none"
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
    default: 3000 
  },
  position: {
    type: String,
    default: 'bottom-4 right-4'
  }
})

const visible = ref(false)
let timeoutId = null

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
