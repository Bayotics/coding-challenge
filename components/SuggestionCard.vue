<template>
  <div class="mt-3">
    <div class="card bg-light">
      <div class="card-header d-flex justify-content-between align-items-center">
        <span>{{ suggestion.type }}</span>
        <button 
          class="btn btn-sm btn-outline-primary" 
          @click="$emit('apply')"
        >
          Apply
        </button>
      </div>
      <div class="card-body">
        <p class="mb-0">{{ suggestion.content }}</p>
      </div>
    </div>
  </div>
</template>

<script setup>
// Props and emits
defineProps({
  suggestion: {
    type: Object,
    required: true
  }
})

defineEmits(['apply'])
</script>
