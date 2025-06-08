<template>
  <div v-if="visible" class="modal fade show d-block" tabindex="-1" style="background-color: rgba(0,0,0,0.5);">
    <div class="modal-dialog modal-dialog-centered">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title d-flex align-items-center">
            <component :is="iconComponent" :class="iconClass" class="me-2" style="width: 1.5rem; height: 1.5rem;" />
            {{ title }}
          </h5>
          <button type="button" class="btn-close" @click="handleCancel"></button>
        </div>
        <div class="modal-body">
          <p class="text-secondary mb-0">{{ message }}</p>
        </div>
        <div class="modal-footer">
          <button 
            @click="handleCancel"
            class="btn btn-secondary"
          >
            {{ cancelText }}
          </button>
          <button 
            @click="handleConfirm"
            :class="[
              'btn',
              type === 'danger' ? 'btn-danger' : 'btn-primary'
            ]"
          >
            {{ confirmText }}
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue'
import { AlertTriangleIcon, FileQuestionIcon } from 'lucide-vue-next'

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
      return FileQuestionIcon
  }
})

const iconClass = computed(() => {
  switch (props.type) {
    case 'danger':
      return 'text-danger'
    case 'warning':
      return 'text-warning'
    default:
      return 'text-primary'
  }
})

const handleConfirm = () => {
  emit('confirm')
}

const handleCancel = () => {
  emit('cancel')
}
</script>
