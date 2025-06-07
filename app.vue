<template>
  <div class="app-container px-6">
    <header class="bg-primary text-white p-4 mb-4">
      <div class="container">
        <h1 class="mb-0 text-gray-800">AI Blog Assistant</h1>
      </div>
    </header>

    <main class="container">
      <div class="row">
        <div class="col-lg-8 mb-4">
          <BlogEditor 
            v-model:blog-post="blogPost"
            @save-draft="handleSaveDraft"
            @load-drafts="showDraftModal"
          />
        </div>

        <div class="col-lg-4">
          <AISuggestionPanel 
            :blog-post="blogPost"
            @apply-suggestion="handleApplySuggestion"
          />
          
          <DraftManager 
            :drafts="savedDrafts"
            @load-draft="handleLoadDraft"
            @delete-draft="handleDeleteDraft"
          />
        </div>
      </div>
    </main>

    <DraftModal 
      v-if="showModal"
      :drafts="savedDrafts"
      @close="showModal = false"
      @load-draft="handleLoadDraft"
    />
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import BlogEditor from './components/BlogEditor.vue'
import AISuggestionPanel from './components/AISuggestionPanel.vue'
import DraftManager from './components/DraftManager.vue'
import DraftModal from './components/DraftModal.vue'
import './assets/css/main.css'

// Application state
const blogPost = ref({
  title: '',
  content: '',
  tags: '',
  timestamp: null
})

const savedDrafts = ref([])
const showModal = ref(false)

// Initialize application
onMounted(() => {
  loadSavedDrafts()
})

// Draft management functions
const loadSavedDrafts = () => {
  const drafts = localStorage.getItem('blogDrafts')
  if (drafts) {
    savedDrafts.value = JSON.parse(drafts)
  }
}

const saveDraftsToStorage = () => {
  localStorage.setItem('blogDrafts', JSON.stringify(savedDrafts.value))
}

// Event handlers
const handleSaveDraft = () => {
  const newDraft = {
    ...blogPost.value,
    timestamp: new Date().toISOString()
  }
  
  savedDrafts.value.unshift(newDraft)
  saveDraftsToStorage()
  
  alert('Draft saved successfully!')
}

const handleLoadDraft = (index) => {
  const draft = savedDrafts.value[index]
  blogPost.value = { ...draft }
  showModal.value = false
}

const handleDeleteDraft = (index) => {
  if (confirm('Are you sure you want to delete this draft?')) {
    savedDrafts.value.splice(index, 1)
    saveDraftsToStorage()
  }
}

const handleApplySuggestion = ({ type, content }) => {
  if (type === 'Title Suggestion') {
    blogPost.value.title = content
  } else if (type === 'Keyword Suggestions') {
    blogPost.value.tags = content
  }
}

const showDraftModal = () => {
  showModal.value = true
}
</script>

