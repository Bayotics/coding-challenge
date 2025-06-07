<template>
  <div class="modal fade show" style="display: block;">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title">Load Draft</h5>
          <button type="button" class="btn-close" @click="$emit('close')"></button>
        </div>
        <div class="modal-body">
          <ul class="list-group" v-if="drafts.length > 0">
            <li 
              v-for="(draft, index) in drafts" 
              :key="index" 
              class="list-group-item list-group-item-action"
              @click="$emit('loadDraft', index)"
              style="cursor: pointer;"
            >
              <div class="d-flex w-100 justify-content-between">
                <h5 class="mb-1">{{ draft.title || 'Untitled Draft' }}</h5>
                <small>{{ formatDate(draft.timestamp) }}</small>
              </div>
              <p class="mb-1 text-truncate">
                {{ getPreviewText(draft.content) }}
              </p>
            </li>
          </ul>
          <p v-else class="text-center text-muted my-3">
            No saved drafts available.
          </p>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-secondary" @click="$emit('close')">
            Close
          </button>
        </div>
      </div>
    </div>
    <div class="modal-backdrop fade show"></div>
  </div>
</template>

<script setup>
// Props and emits
defineProps({
  drafts: {
    type: Array,
    required: true
  }
})

defineEmits(['close', 'loadDraft'])

// Utility functions
const formatDate = (dateString) => {
  const date = new Date(dateString)
  return date.toLocaleDateString() + ' ' + date.toLocaleTimeString()
}

const stripHtml = (html) => {
  const tmp = document.createElement('DIV')
  tmp.innerHTML = html
  return tmp.textContent || tmp.innerText || ''
}

const getPreviewText = (content) => {
  const text = stripHtml(content)
  return text.length > 100 ? text.substring(0, 100) + '...' : text
}
</script>

<style scoped>
.modal-backdrop {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  z-index: 1040;
}

.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 1050;
  overflow-x: hidden;
  overflow-y: auto;
}
</style>
