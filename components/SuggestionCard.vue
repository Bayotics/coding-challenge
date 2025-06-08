<template>
  <div class="card bg-light">
    <div class="card-header d-flex justify-content-between align-items-center bg-light">
      <span class="text-secondary">{{ suggestion.type }}</span>
      <button 
        @click="$emit('apply')"
        class="btn btn-sm btn-primary"
        v-if="!isErrorMessage"
      >
        Apply
      </button>
    </div>
    <div class="card-body">
      <p class="card-text text-secondary" style="white-space: pre-wrap;">{{ suggestion.content }}</p>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue'

// Props and emits
const props = defineProps({
  suggestion: {
    type: Object,
    required: true
  }
})

// Check if the current suggestion is an error message
const isErrorMessage = computed(() => {
  const content = props.suggestion.content.toLowerCase()
  return content.includes('error') || 
         content.includes('missing') || 
         content.includes('invalid') || 
         content.includes('failed') ||
         content.includes('api key')
})

defineEmits(['apply'])
</script>
