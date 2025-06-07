<template>
  <div class="card shadow-sm">
    <div class="card-header">
      <h2 class="h5 mb-0">Saved Drafts</h2>
    </div>
    <div class="card-body">
      <ul class="list-group" v-if="drafts.length > 0">
        <li 
          v-for="(draft, index) in drafts" 
          :key="index" 
          class="list-group-item d-flex justify-content-between align-items-center"
        >
          <span class="text-truncate me-2">
            {{ draft.title || 'Untitled Draft' }}
          </span>
          <div>
            <button 
              class="btn btn-sm btn-outline-primary me-1" 
              @click="$emit('loadDraft', index)"
              title="Load Draft"
            >
              <EditIcon size="14" />
            </button>
            <button 
              class="btn btn-sm btn-outline-danger" 
              @click="$emit('deleteDraft', index)"
              title="Delete Draft"
            >
              <Trash2Icon size="14" />
            </button>
          </div>
        </li>
      </ul>
      <p v-else class="text-muted mb-0">No saved drafts yet.</p>
    </div>
  </div>
</template>

<script setup>
import { EditIcon, Trash2Icon } from 'lucide-vue-next'

// Props and emits
defineProps({
  drafts: {
    type: Array,
    required: true
  }
})

defineEmits(['loadDraft', 'deleteDraft'])
</script>
