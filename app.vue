<template>
  <div class="d-flex vh-100 bg-light overflow-hidden">
    <!-- Sidebar -->
    <div class="bg-dark text-white flex-shrink-0 overflow-auto d-none d-md-block" style="width: 16rem;">
      <div class="p-3 border-bottom border-secondary">
        <h1 class="h5 fw-bold mb-0">AI Blog Assistant</h1>
      </div>
      
      <!-- Drafts Navigation -->
      <div class="py-2">
        <div class="px-3 py-2 d-flex align-items-center justify-content-between">
          <h2 class="text-uppercase small fw-semibold text-secondary mb-0">Saved Drafts</h2>
          <button 
            @click="handleNewDraft" 
            class="btn btn-sm btn-primary d-flex align-items-center"
          >
            <PlusIcon class="me-1" style="width: 0.75rem; height: 0.75rem;" />
            New
          </button>
        </div>
        
        <div v-if="savedDrafts.length === 0" class="px-3 py-2 text-secondary small">
          No drafts saved yet
        </div>
        
        <ul class="list-unstyled mb-0">
          <li 
            v-for="(draft, index) in savedDrafts" 
            :key="index"
            :class="[
              'px-3 py-2 small cursor-pointer d-flex align-items-center',
              currentDraftIndex === index ? 'bg-primary text-white' : 'text-white hover-bg-secondary'
            ]"
            @click="handleLoadDraft(index)"
            style="cursor: pointer;"
          >
            <FileTextIcon class="me-2 flex-shrink-0" style="width: 1rem; height: 1rem;" />
            <span class="text-truncate flex-grow-1">{{ draft.title || 'Untitled Draft' }}</span>
            <button 
              @click.stop="confirmDeleteDraft(index)" 
              class="btn btn-sm btn-link text-white p-0 opacity-0 hover-opacity-100"
              style="transition: opacity 0.2s;"
            >
              <TrashIcon style="width: 1rem; height: 1rem;" />
            </button>
          </li>
        </ul>
        
        <!-- Storage Warning -->
        <div v-if="storageWarning" class="px-3 py-2 mt-2">
          <div class="bg-warning-subtle text-warning-emphasis small p-2 rounded">
            Storage almost full. Consider deleting old drafts.
          </div>
        </div>
      </div>
    </div>
    
    <!-- Mobile Sidebar Toggle -->
    <div class="d-md-none position-fixed bottom-0 start-0 mb-4 ms-4" style="z-index: 1030;">
      <button 
        @click="showMobileSidebar = !showMobileSidebar" 
        class="btn btn-primary rounded-circle shadow"
        style="width: 3rem; height: 3rem;"
      >
        <MenuIcon v-if="!showMobileSidebar" style="width: 1.25rem; height: 1.25rem;" />
        <XIcon v-else style="width: 1.25rem; height: 1.25rem;" />
      </button>
    </div>
    
    <!-- Mobile Sidebar -->
    <div 
      v-if="showMobileSidebar" 
      class="position-fixed top-0 start-0 bottom-0 end-0 bg-dark text-white d-md-none"
      style="z-index: 1020;"
    >
      <div class="p-3 border-bottom border-secondary d-flex justify-content-between align-items-center">
        <h1 class="h5 fw-bold mb-0">AI Blog Assistant</h1>
        <button @click="showMobileSidebar = false" class="btn btn-link text-secondary p-0">
          <XIcon style="width: 1.5rem; height: 1.5rem;" />
        </button>
      </div>
      
      <!-- Mobile Drafts Navigation -->
      <div class="py-2">
        <div class="px-3 py-2 d-flex align-items-center justify-content-between">
          <h2 class="text-uppercase small fw-semibold text-secondary mb-0">Saved Drafts</h2>
          <button 
            @click="handleNewDraftMobile" 
            class="btn btn-sm btn-primary d-flex align-items-center"
          >
            <PlusIcon class="me-1" style="width: 0.75rem; height: 0.75rem;" />
            New
          </button>
        </div>
        
        <div v-if="savedDrafts.length === 0" class="px-3 py-2 text-secondary small">
          No drafts saved yet
        </div>
        
        <ul class="list-unstyled mb-0">
          <li 
            v-for="(draft, index) in savedDrafts" 
            :key="index"
            :class="[
              'px-3 py-2 small cursor-pointer d-flex align-items-center',
              currentDraftIndex === index ? 'bg-primary text-white' : 'text-white hover-bg-secondary'
            ]"
            @click="handleLoadDraft(index); showMobileSidebar = false"
            style="cursor: pointer;"
          >
            <FileTextIcon class="me-2 flex-shrink-0" style="width: 1rem; height: 1rem;" />
            <span class="text-truncate flex-grow-1">{{ draft.title || 'Untitled Draft' }}</span>
            <button 
              @click.stop="confirmDeleteDraft(index)" 
              class="btn btn-sm btn-link text-white p-0"
            >
              <TrashIcon style="width: 1rem; height: 1rem;" />
            </button>
          </li>
        </ul>
        
        <!-- Mobile Storage Warning -->
        <div v-if="storageWarning" class="px-3 py-2 mt-2">
          <div class="bg-warning-subtle text-warning-emphasis small p-2 rounded">
            Storage almost full. Consider deleting old drafts.
          </div>
        </div>
      </div>
    </div>

    <!-- Main Content -->
    <div class="flex-grow-1 d-flex flex-column overflow-hidden">
      <!-- Header -->
      <header class="bg-white border-bottom shadow-sm">
        <div class="d-flex align-items-center justify-content-between px-3 py-2">
          <div class="d-flex align-items-center">
            <MenuIcon class="me-2 d-md-none text-secondary" style="width: 1.25rem; height: 1.25rem;" @click="showMobileSidebar = !showMobileSidebar" />
            <h1 class="h5 fw-medium text-dark mb-0">{{ blogPost.title || 'Untitled Draft' }}</h1>
          </div>
          <div class="d-flex align-items-center">
            <button 
              @click="confirmSaveDraft" 
              class="btn btn-primary d-flex align-items-center"
            >
              <SaveIcon class="me-1" style="width: 1rem; height: 1rem;" />
              Save Draft
            </button>
          </div>
        </div>
      </header>
      
      <!-- Content Area -->
      <div class="flex-grow-1 overflow-auto">
        <div class="container-fluid py-4">
          <div class="row g-4">
            <!-- Blog Editor -->
            <div class="col-lg-8">
              <BlogEditor 
                v-model:blog-post="blogPost"
                :key="editorKey"
                @save-draft="confirmSaveDraft"
              />
            </div>
            
            <!-- AI Suggestions Panel -->
            <div class="col-lg-4">
              <AISuggestionPanel 
                ref="aiSuggestionPanelRef"
                :blog-post="blogPost"
                @apply-suggestion="handleApplySuggestion"
              />
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Storage Error Modal -->
    <div v-if="showStorageError" class="modal fade show d-block" tabindex="-1" style="background-color: rgba(0,0,0,0.5);">
      <div class="modal-dialog modal-dialog-centered">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title d-flex align-items-center">
              <AlertTriangleIcon class="text-danger me-2" style="width: 1.5rem; height: 1.5rem;" />
              Storage Full
            </h5>
            <button type="button" class="btn-close" @click="showStorageError = false"></button>
          </div>
          <div class="modal-body">
            <p class="text-secondary mb-0">
              Your browser storage is full. Please delete some old drafts to save new ones.
            </p>
          </div>
          <div class="modal-footer">
            <button 
              @click="showStorageError = false" 
              class="btn btn-secondary"
            >
              Cancel
            </button>
            <button 
              @click="cleanupOldDrafts" 
              class="btn btn-danger"
            >
              Delete Old Drafts
            </button>
          </div>
        </div>
      </div>
    </div>
    
    <!-- Confirmation Dialog -->
    <ConfirmationDialog
      :visible="confirmationDialog.visible"
      :title="confirmationDialog.title"
      :message="confirmationDialog.message"
      :type="confirmationDialog.type"
      :confirm-text="confirmationDialog.confirmText"
      :cancel-text="confirmationDialog.cancelText"
      @confirm="handleConfirmationConfirm"
      @cancel="handleConfirmationCancel"
    />
    
    <!-- Toast Container -->
    <ToastContainer ref="toastContainerRef" />
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue'
import { 
  MenuIcon, 
  XIcon, 
  PlusIcon, 
  SaveIcon, 
  FileTextIcon, 
  TrashIcon,
  AlertTriangleIcon
} from 'lucide-vue-next'
import BlogEditor from './components/BlogEditor.vue'
import AISuggestionPanel from './components/AISuggestionPanel.vue'
import ToastContainer from './components/ToastContainer.vue'
import ConfirmationDialog from './components/ConfirmationDialog.vue'

// Application state
const blogPost = ref({
  title: '',
  content: '',
  tags: '',
  timestamp: null
})

const savedDrafts = ref([])
const showMobileSidebar = ref(false)
const currentDraftIndex = ref(-1)
const editorKey = ref(0) // Key to force re-render of editor
const showStorageError = ref(false)
const aiSuggestionPanelRef = ref(null)
const toastContainerRef = ref(null)

// Confirmation dialog state
const confirmationDialog = ref({
  visible: false,
  title: '',
  message: '',
  type: 'info',
  confirmText: 'Confirm',
  cancelText: 'Cancel',
  action: null,
  data: null
})

// Storage management
const MAX_DRAFTS = 50 // Maximum number of drafts to keep
const storageWarning = computed(() => {
  return savedDrafts.value.length > 40 // Show warning when approaching limit
})

// Initialize application
onMounted(() => {
  loadSavedDrafts()
})

// Toast notification methods
const showToast = (message, type = 'success') => {
  if (toastContainerRef.value) {
    toastContainerRef.value.addToast({
      message,
      type,
      duration: 3000,
      position: 'bottom-4 right-4'
    })
  }
}

// Confirmation dialog methods
const showConfirmationDialog = (options) => {
  confirmationDialog.value = {
    visible: true,
    title: options.title,
    message: options.message,
    type: options.type || 'info',
    confirmText: options.confirmText || 'Confirm',
    cancelText: options.cancelText || 'Cancel',
    action: options.action,
    data: options.data
  }
}

const handleConfirmationConfirm = () => {
  const { action, data } = confirmationDialog.value
  confirmationDialog.value.visible = false
  
  if (action && typeof action === 'function') {
    action(data)
  }
}

const handleConfirmationCancel = () => {
  confirmationDialog.value.visible = false
}

// Draft management functions
const loadSavedDrafts = () => {
  try {
    const drafts = localStorage.getItem('blogDrafts')
    if (drafts) {
      savedDrafts.value = JSON.parse(drafts)
      // Ensure we don't exceed the maximum number of drafts
      if (savedDrafts.value.length > MAX_DRAFTS) {
        savedDrafts.value = savedDrafts.value.slice(0, MAX_DRAFTS)
        saveDraftsToStorage()
      }
    }
  } catch (error) {
    console.error('Error loading drafts:', error)
    savedDrafts.value = []
    showToast('Failed to load drafts from storage', 'error')
  }
}

const saveDraftsToStorage = () => {
  try {
    // Limit the number of drafts to prevent storage overflow
    const draftsToSave = savedDrafts.value.slice(0, MAX_DRAFTS)
    localStorage.setItem('blogDrafts', JSON.stringify(draftsToSave))
    savedDrafts.value = draftsToSave
    return true
  } catch (error) {
    console.error('Storage error:', error)
    if (error.name === 'QuotaExceededError') {
      showStorageError.value = true
    }
    return false
  }
}

// Check available storage space
const getStorageSize = () => {
  let total = 0
  for (let key in localStorage) {
    if (localStorage.hasOwnProperty(key)) {
      total += localStorage[key].length + key.length
    }
  }
  return total
}

// Clean up old drafts
const cleanupOldDrafts = () => {
  // Keep only the 20 most recent drafts
  savedDrafts.value = savedDrafts.value
    .sort((a, b) => new Date(b.timestamp) - new Date(a.timestamp))
    .slice(0, 20)
  
  const success = saveDraftsToStorage()
  showStorageError.value = false
  
  // Reset current draft index if it was affected
  if (currentDraftIndex.value >= savedDrafts.value.length) {
    currentDraftIndex.value = -1
  }
  
  if (success) {
    showToast('Old drafts cleaned up successfully', 'success')
  } else {
    showToast('Failed to clean up drafts', 'error')
  }
}

// Check if current draft has content
const hasContent = () => {
  const content = blogPost.value.content || ''
  // Strip HTML and check if there's actual text content
  const textContent = content.replace(/<[^>]*>/g, '').trim()
  return textContent.length > 0
}

// Event handlers
const confirmSaveDraft = () => {
  // Don't save empty drafts
  if (!hasContent()) {
    showToast('Cannot save empty draft', 'error')
    return
  }

  const draftTitle = blogPost.value.title || 'Untitled Draft'
  const isUpdate = currentDraftIndex.value >= 0
  
  showConfirmationDialog({
    title: isUpdate ? 'Update Draft' : 'Save Draft',
    message: isUpdate 
      ? `Are you sure you want to update the draft "${draftTitle}"?`
      : `Are you sure you want to save this draft as "${draftTitle}"?`,
    type: 'info',
    confirmText: isUpdate ? 'Update' : 'Save',
    cancelText: 'Cancel',
    action: handleSaveDraft
  })
}

const handleSaveDraft = () => {
  const newDraft = {
    ...blogPost.value,
    timestamp: new Date().toISOString()
  }
  
  if (currentDraftIndex.value >= 0) {
    // Update existing draft
    savedDrafts.value[currentDraftIndex.value] = newDraft
  } else {
    // Add new draft at the beginning
    savedDrafts.value.unshift(newDraft)
    currentDraftIndex.value = 0
  }
  
  const success = saveDraftsToStorage()
  
  if (success) {
    showToast('Draft saved successfully', 'success')
  } else {
    showToast('Failed to save draft', 'error')
  }
}

const handleLoadDraft = (index) => {
  const draft = savedDrafts.value[index]
  blogPost.value = { ...draft }
  currentDraftIndex.value = index
  // Force re-render of editor to ensure content is updated
  editorKey.value++
  showToast('Draft loaded', 'success')
}

const confirmDeleteDraft = (index) => {
  const draft = savedDrafts.value[index]
  const draftTitle = draft.title || 'Untitled Draft'
  
  showConfirmationDialog({
    title: 'Delete Draft',
    message: `Are you sure you want to delete the draft "${draftTitle}"? This action cannot be undone.`,
    type: 'danger',
    confirmText: 'Delete',
    cancelText: 'Cancel',
    action: handleDeleteDraft,
    data: index
  })
}

const handleDeleteDraft = (index) => {
  try {
    savedDrafts.value.splice(index, 1)
    const success = saveDraftsToStorage()
    
    if (currentDraftIndex.value === index) {
      // If we deleted the current draft, reset
      currentDraftIndex.value = -1
      blogPost.value = {
        title: '',
        content: '',
        tags: '',
        timestamp: null
      }
      // Force re-render of editor
      editorKey.value++
    } else if (currentDraftIndex.value > index) {
      // Adjust current index if needed
      currentDraftIndex.value--
    }
    
    if (success) {
      showToast('Draft deleted successfully', 'success')
    } else {
      showToast('Draft deleted but failed to update storage', 'error')
    }
  } catch (error) {
    console.error('Error deleting draft:', error)
    showToast('Failed to delete draft', 'error')
  }
}

const handleNewDraft = () => {
  // Save current draft if it has content
  if (hasContent()) {
    handleSaveDraft()
  }
  
  // Reset the current draft
  blogPost.value = {
    title: '',
    content: '',
    tags: '',
    timestamp: null
  }
  currentDraftIndex.value = -1
  
  // Clear AI suggestions
  clearAISuggestions()
  
  // Force re-render of editor to ensure content is cleared
  editorKey.value++
  
  showToast('New draft created', 'success')
}

const handleNewDraftMobile = () => {
  handleNewDraft();
  showMobileSidebar.value = false;
}

const handleApplySuggestion = ({ type, content }) => {
  if (type === 'Title Suggestion') {
    blogPost.value.title = content
    showToast('Title applied successfully', 'success')
  } else if (type === 'Keyword Suggestions') {
    // Clean the tags response
    const cleanedTags = cleanTagsResponse(content)
    blogPost.value.tags = cleanedTags
    showToast('Keywords applied successfully', 'success')
  } else if (type === 'Summary Suggestion') {
    // Note: Summary application logic can be added here when needed
    showToast('Summary applied successfully', 'success')
  }
}

const cleanTagsResponse = (rawResponse) => {
  // Split the response into lines
  const lines = rawResponse.split('\n')
  
  // Filter out empty lines and the introductory paragraph
  const tagLines = lines.filter(line => {
    const trimmed = line.trim()
    // Skip empty lines, introductory text, and lines that don't look like numbered items
    return trimmed && 
           !trimmed.toLowerCase().includes('here are') &&
           !trimmed.toLowerCase().includes('keywords') &&
           !trimmed.toLowerCase().includes('tags') &&
           (trimmed.match(/^\d+\./) || trimmed.match(/^-/) || trimmed.match(/^\*/) || trimmed.match(/^\w/))
  })
  
  // Extract just the tag content, removing numbers, bullets, and extra formatting
  const cleanedTags = tagLines.map(line => {
    return line
      .replace(/^\d+\.\s*/, '') // Remove "1. ", "2. ", etc.
      .replace(/^-\s*/, '')     // Remove "- "
      .replace(/^\*\s*/, '')    // Remove "* "
      .replace(/^•\s*/, '')     // Remove "• "
      .trim()
  }).filter(tag => tag.length > 0) // Remove any empty results
  
  // Join with commas and return
  return cleanedTags.join(', ')
}

const clearAISuggestions = () => {
  if (aiSuggestionPanelRef.value) {
    aiSuggestionPanelRef.value.clearSuggestions()
  }
}
</script>

<style>
/* Add Bootstrap hover effect for sidebar items */
.hover-bg-secondary:hover {
  background-color: rgba(255, 255, 255, 0.1) !important;
}

/* Add hover effect for buttons */
.hover-opacity-100:hover {
  opacity: 1 !important;
}
</style>
