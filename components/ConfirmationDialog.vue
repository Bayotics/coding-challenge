<template>
  <div v-if="visible" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
    <div class="bg-white rounded-lg p-6 max-w-md mx-4 shadow-xl">
      <div class="flex items-center mb-4">
        <component :is="iconComponent" :class="iconClass" class="w-6 h-6 mr-3" />
        <h3 class="text-lg font-semibold text-gray-900">{{ title }}</h3>
      </div>
      
      <p class="text-gray-600 mb-6">{{ message }}</p>
      
      <div class="flex justify-end space-x-3">
        <button 
          @click="handleCancel"
          class="px-4 py-2 text-gray-600 hover:text-gray-800 font-medium"
        >
          {{ cancelText }}
        </button>
        <button 
          @click="handleConfirm"
          :class="[
            'px-4 py-2 rounded font-medium',
            type === 'danger' 
              ? 'bg-red-600 hover:bg-red-700 text-white' 
              : 'bg-blue-600 hover:bg-blue-700 text-white'
          ]"
        >
          {{ confirmText }}
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue'
import { AlertTriangleIcon, MessageCircleQuestionIcon, SaveIcon } from 'lucide-vue-next'

const props = defineProps({
  visible: {
    type: Boolean,
    default: false
  },
  title: {
    type: String,
    required: true
  },
  message: {
    type: String,
    required: true
  },
  type: {
    type: String,
    default: 'info',
    validator: (value) => ['info', 'danger', 'warning'].includes(value)
  },
  confirmText: {
    type: String,
    default: 'Confirm'
  },
  cancelText: {
    type: String,
    default: 'Cancel'
  }
})

const emit = defineEmits(['confirm', 'cancel'])

const iconComponent = computed(() => {
  switch (props.type) {
    case 'danger':
      return AlertTriangleIcon
    case 'warning':
      return AlertTriangleIcon
    default:
      return MessageCircleQuestionIcon
  }
})

const iconClass = computed(() => {
  switch (props.type) {
    case 'danger':
      return 'text-red-500'
    case 'warning':
      return 'text-yellow-500'
    default:
      return 'text-blue-500'
  }
})

const handleConfirm = () => {
  emit('confirm')
}

const handleCancel = () => {
  emit('cancel')
}
</script>
