<template>
  <div class="card">
    <div class="card-header bg-light">
      <h5 class="card-title mb-0 small">AI Suggestions</h5>
    </div>
    
    <!-- API Key Warning (only shown if API key is missing) -->
    <div v-if="!hasApiKey" class="alert alert-warning m-3 mb-0">
      <div class="d-flex">
        <div class="me-2">
          <AlertTriangleIcon class="h-5 w-5 text-warning" />
        </div>
        <div>
          <h6 class="alert-heading">Missing API Key</h6>
          <p class="mb-1">Please add your OpenAI API key to the .env file.</p>
          <p class="mb-0 small">
            Add <code class="bg-warning-subtle px-1 py-0.5 rounded">VITE_OPENAI_API_KEY=your_api_key_here</code> to your .env file and restart the server.
          </p>
        </div>
      </div>
    </div>

    <!-- AI Suggestion Buttons -->
    <div class="card-body">
      <div class="d-grid gap-2">
        <button 
          class="btn btn-primary d-flex align-items-center justify-content-center"
          @click="generateTitle" 
          :disabled="isGenerating || !hasContent || !hasApiKey"
        >
          <SparklesIcon class="me-2" style="width: 1rem; height: 1rem;" />
          Generate Title
        </button>
        <button 
          class="btn btn-primary d-flex align-items-center justify-content-center"
          @click="generateSummary" 
          :disabled="isGenerating || !hasContent || !hasApiKey"
        >
          <FileTextIcon class="me-2" style="width: 1rem; height: 1rem;" />
          Summarize Content
        </button>
        <button 
          class="btn btn-primary d-flex align-items-center justify-content-center"
          @click="generateKeywords" 
          :disabled="isGenerating || !hasContent || !hasApiKey"
        >
          <TagIcon class="me-2" style="width: 1rem; height: 1rem;" />
          Suggest Keywords
        </button>
      </div>

      <div v-if="isGenerating" class="mt-4 text-center">
        <div class="d-flex flex-column align-items-center">
          <div class="spinner-border text-primary" role="status"></div>
          <p class="mt-2 text-secondary">Generating suggestions...</p>
        </div>
      </div>

      <div v-if="currentSuggestion.content" class="mt-4">
        <div class="card bg-light">
          <div class="card-header d-flex justify-content-between align-items-center bg-light">
            <span class="text-secondary">{{ currentSuggestion.type }}</span>
            <button 
              v-if="!isErrorMessage"
              @click="handleApplySuggestion"
              class="btn btn-sm btn-primary"
            >
              Apply
            </button>
          </div>
          <div class="card-body">
            <p class="card-text text-secondary" style="white-space: pre-wrap;">{{ currentSuggestion.content }}</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import { generateText } from 'ai'
import { createOpenAI } from '@ai-sdk/openai'
import { SparklesIcon, FileTextIcon, TagIcon, AlertTriangleIcon } from 'lucide-vue-next'

// Get API key from environment variables
const OPENAI_API_KEY = import.meta.env.VITE_OPENAI_API_KEY || ''

// Create OpenAI client
const openai = createOpenAI({
  apiKey: OPENAI_API_KEY
})

// Props and emits
const props = defineProps({
  blogPost: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['applySuggestion'])

// Component state
const isGenerating = ref(false)
const currentSuggestion = ref({
  type: '',
  content: ''
})

// Computed properties
const hasContent = computed(() => {
  return props.blogPost.content && stripHtml(props.blogPost.content).trim().length > 0
})

const hasApiKey = computed(() => {
  return OPENAI_API_KEY && OPENAI_API_KEY.trim() !== ''
})

// Check if the current suggestion is an error message
const isErrorMessage = computed(() => {
  const content = currentSuggestion.value.content.toLowerCase()
  return content.includes('error') || 
         content.includes('missing') || 
         content.includes('invalid') || 
         content.includes('failed') ||
         content.includes('api key')
})

// Utility functions
const stripHtml = (html) => {
  const tmp = document.createElement('DIV')
  tmp.innerHTML = html
  return tmp.textContent || tmp.innerText || ''
}

const generateAISuggestion = async (type, prompt, systemMessage) => {
  if (!hasApiKey.value) {
    currentSuggestion.value = { 
      type, 
      content: 'OpenAI API key is missing. Please add it to your .env file.' 
    }
    return
  }

  isGenerating.value = true
  currentSuggestion.value = { type: '', content: '' }
  
  try {
    const content = stripHtml(props.blogPost.content)
    
    const { text } = await generateText({
      model: openai('gpt-4o-mini'),
      prompt: `${prompt}: "${content.substring(0, 1500)}..."`,
      system: systemMessage
    })
    
    currentSuggestion.value = { type, content: text }
  } catch (error) {
    console.error(`Error generating ${type.toLowerCase()}:`, error)
    
    if (error.message?.includes('API key') || error.message?.includes('401')) {
      currentSuggestion.value = { 
        type, 
        content: 'Invalid API key. Please check your OpenAI API key in the .env file.' 
      }
    } else {
      currentSuggestion.value = { 
        type, 
        content: `Error generating ${type.toLowerCase()}. Please try again.` 
      }
    }
  } finally {
    isGenerating.value = false
  }
}

// AI generation methods
const generateTitle = () => {
  generateAISuggestion(
    'Title Suggestion',
    'Generate a catchy, SEO-friendly title for this blog post content',
    'You are a professional blog editor who specializes in creating engaging titles.'
  )
}

const generateSummary = () => {
  generateAISuggestion(
    'Summary Suggestion',
    'Create a concise summary (2-3 sentences) for this blog post',
    'You are a professional editor who creates concise, engaging summaries.'
  )
}

const generateKeywords = () => {
  generateAISuggestion(
    'Keyword Suggestions',
    'Generate 5-8 relevant SEO keywords or tags for this blog post',
    'You are an SEO specialist who identifies the most relevant keywords for content.'
  )
}

const handleApplySuggestion = () => {
  emit('applySuggestion', currentSuggestion.value)
}

const clearSuggestions = () => {
  currentSuggestion.value = {
    type: '',
    content: ''
  }
  isGenerating.value = false
}

// Expose the clearSuggestions method so parent can call it
defineExpose({
  clearSuggestions
})
</script>
