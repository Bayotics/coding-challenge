<template>
  <div class="bg-gray-50 border border-gray-200 rounded-md overflow-hidden">
    <div class="bg-gray-100 px-4 py-2 border-b border-gray-200 flex justify-between items-center">
      <span class="text-sm font-medium text-gray-700">{{ suggestion.type }}</span>
      <button 
        @click="$emit('apply')"
        class="text-xs bg-blue-600 hover:bg-blue-700 text-white px-2 py-1 rounded"
      >
        Apply
      </button>
    </div>
    <div class="p-4">
      <p class="text-sm text-gray-800 whitespace-pre-wrap">{{ suggestion.content }}</p>
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
