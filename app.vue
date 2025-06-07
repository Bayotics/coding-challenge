<template>
  <div class="flex h-screen bg-gray-100 overflow-hidden">
    <!-- Sidebar -->
    <div class="w-64 bg-gray-900 text-white flex-shrink-0 overflow-y-auto hidden md:block">
      <div class="p-4 border-b border-gray-800">
        <h1 class="text-xl font-bold">AI Blog Assistant</h1>
      </div>
      
      <!-- Drafts Navigation -->
      <div class="py-2">
        <div class="px-4 py-2 flex items-center justify-between">
          <h2 class="text-sm font-semibold text-gray-400 uppercase">Saved Drafts</h2>
          <button 
            @click="handleNewDraft" 
            class="text-xs bg-blue-600 hover:bg-blue-700 text-white py-1 px-2 rounded flex items-center"
          >
            <PlusIcon class="w-3 h-3 mr-1" />
            New
          </button>
        </div>
        
        <div v-if="savedDrafts.length === 0" class="px-4 py-3 text-sm text-gray-400">
          No drafts saved yet
        </div>
        
        <ul v-else class="space-y-1">
          <li 
            v-for="(draft, index) in savedDrafts" 
            :key="index"
            :class="[
              'px-4 py-2 text-sm cursor-pointer flex items-center group',
              currentDraftIndex === index ? 'bg-blue-800 text-white' : 'hover:bg-gray-800'
            ]"
            @click="handleLoadDraft(index)"
          >
            <FileTextIcon class="w-4 h-4 mr-2 flex-shrink-0" />
            <span class="truncate flex-grow">{{ draft.title || 'Untitled Draft' }}</span>
            <button 
              @click.stop="confirmDeleteDraft(index)" 
              class="text-gray-400 hover:text-white opacity-0 group-hover:opacity-100 transition-opacity"
            >
              <TrashIcon class="w-4 h-4" />
            </button>
          </li>
        </ul>
        
        <!-- Storage Warning -->
        <div v-if="storageWarning" class="px-4 py-2 mt-2">
          <div class="bg-yellow-800 text-yellow-200 text-xs p-2 rounded">
            Storage almost full. Consider deleting old drafts.
          </div>
        </div>
      </div>
    </div>
    
    <!-- Mobile Sidebar Toggle -->
    <div class="md:hidden fixed bottom-4 left-4 z-50">
      <button 
        @click="showMobileSidebar = !showMobileSidebar" 
        class="bg-blue-600 text-white p-3 rounded-full shadow-lg"
      >
        <MenuIcon v-if="!showMobileSidebar" class="w-5 h-5" />
        <XIcon v-else class="w-5 h-5" />
      </button>
    </div>
    
    <!-- Mobile Sidebar -->
    <div 
      v-if="showMobileSidebar" 
      class="fixed inset-0 bg-gray-900 text-white z-40 md:hidden"
    >
      <div class="p-4 border-b border-gray-800 flex justify-between items-center">
        <h1 class="text-xl font-bold">AI Blog Assistant</h1>
        <button @click="showMobileSidebar = false" class="text-gray-400">
          <XIcon class="w-6 h-6" />
        </button>
      </div>
      
      <!-- Mobile Drafts Navigation -->
      <div class="py-2">
        <div class="px-4 py-2 flex items-center justify-between">
          <h2 class="text-sm font-semibold text-gray-400 uppercase">Saved Drafts</h2>
          <button 
            @click="handleNewDraft; showMobileSidebar = false" 
            class="text-xs bg-blue-600 hover:bg-blue-700 text-white py-1 px-2 rounded flex items-center"
          >
            <PlusIcon class="w-3 h-3 mr-1" />
            New
          </button>
        </div>
        
        <div v-if="savedDrafts.length === 0" class="px-4 py-3 text-sm text-gray-400">
          No drafts saved yet
        </div>
        
        <ul v-else class="space-y-1">
          <li 
            v-for="(draft, index) in savedDrafts" 
            :key="index"
            :class="[
              'px-4 py-2 text-sm cursor-pointer flex items-center',
              currentDraftIndex === index ? 'bg-blue-800 text-white' : 'hover:bg-gray-800'
            ]"
            @click="handleLoadDraft(index); showMobileSidebar = false"
          >
            <FileTextIcon class="w-4 h-4 mr-2 flex-shrink-0" />
            <span class="truncate flex-grow">{{ draft.title || 'Untitled Draft' }}</span>
            <button 
              @click.stop="confirmDeleteDraft(index)" 
              class="text-gray-400 hover:text-white"
            >
              <TrashIcon class="w-4 h-4" />
            </button>
          </li>
        </ul>
        
        <!-- Mobile Storage Warning -->
        <div v-if="storageWarning" class="px-4 py-2 mt-2">
          <div class="bg-yellow-800 text-yellow-200 text-xs p-2 rounded">
            Storage almost full. Consider deleting old drafts.
          </div>
        </div>
      </div>
    </div>

    <!-- Main Content -->
    <div class="flex-1 flex flex-col overflow-hidden">
      <!-- Header -->
      <header class="bg-white border-b border-gray-200 shadow-sm">
        <div class="flex items-center justify-between px-4 py-3">
          <div class="flex items-center space-x-2">
            <MenuIcon class="w-5 h-5 text-gray-500 md:hidden" @click="showMobileSidebar = !showMobileSidebar" />
            <h1 class="text-lg font-medium text-gray-800">{{ blogPost.title || 'Untitled Draft' }}</h1>
          </div>
          <div class="flex items-center space-x-2">
            <button 
              @click="confirmSaveDraft" 
              class="bg-blue-600 hover:bg-blue-700 text-white px-3 py-1.5 rounded text-sm flex items-center"
            >
              <SaveIcon class="w-4 h-4 mr-1" />
              Save Draft
            </button>
          </div>
        </div>
      </header>
      
      <!-- Content Area -->
      <div class="flex-1 overflow-auto">
        <div class="container mx-auto p-4">
          <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
            <!-- Blog Editor -->
            <div class="lg:col-span-2">
              <BlogEditor 
                v-model:blog-post="blogPost"
                :key="editorKey"
                @save-draft="confirmSaveDraft"
              />
            </div>
            
            <!-- AI Suggestions Panel -->
            <div>
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
    <div v-if="showStorageError" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
      <div class="bg-white rounded-lg p-6 max-w-md mx-4">
        <div class="flex items-center mb-4">
          <AlertTriangleIcon class="w-6 h-6 text-red-500 mr-2" />
          <h3 class="text-lg font-semibold">Storage Full</h3>
        </div>
        <p class="text-gray-600 mb-4">
          Your browser storage is full. Please delete some old drafts to save new ones.
        </p>
        <div class="flex justify-end space-x-2">
          <button 
            @click="showStorageError = false" 
            class="px-4 py-2 text-gray-600 hover:text-gray-800"
          >
            Cancel
          </button>
          <button 
            @click="cleanupOldDrafts" 
            class="px-4 py-2 bg-red-600 text-white rounded hover:bg-red-700"
          >
            Delete Old Drafts
          </button>
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

// Add this new function to clean tags response
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
