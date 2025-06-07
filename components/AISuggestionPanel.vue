<template>
  <div class="card shadow-sm mb-4">
    <div class="card-header">
      <h2 class="h5 mb-0">AI Suggestions</h2>
    </div>
    
    <!-- API Key Warning (only shown if API key is missing) -->
    <div v-if="!hasApiKey" class="alert alert-warning m-3">
      <strong>Missing API Key:</strong> Please add your OpenAI API key to the .env file.
      <div class="mt-2">
        <small>
          Add <code>VITE_OPENAI_API_KEY=your_api_key_here</code> to your .env file and restart the server.
        </small>
      </div>
    </div>

    <!-- AI Suggestion Buttons -->
    <div class="card-body">
      <div class="d-grid gap-2">
        <button 
          class="btn btn-primary" 
          @click="generateTitle" 
          :disabled="isGenerating || !hasContent || !hasApiKey"
        >
          <SparklesIcon class="me-1" size="16" />
          Generate Title
        </button>
        <button 
          class="btn btn-primary" 
          @click="generateSummary" 
          :disabled="isGenerating || !hasContent || !hasApiKey"
        >
          <FileTextIcon class="me-1" size="16" />
          Summarize Content
        </button>
        <button 
          class="btn btn-primary" 
          @click="generateKeywords" 
          :disabled="isGenerating || !hasContent || !hasApiKey"
        >
          <TagIcon class="me-1" size="16" />
          Suggest Keywords
        </button>
      </div>

      <LoadingSpinner v-if="isGenerating" />

      <SuggestionCard 
        v-if="currentSuggestion.content"
        :suggestion="currentSuggestion"
        @apply="handleApplySuggestion"
      />
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import { generateText } from 'ai'
import { createOpenAI } from '@ai-sdk/openai'
import { SparklesIcon, FileTextIcon, TagIcon } from 'lucide-vue-next'
import LoadingSpinner from './LoadingSpinner.vue'
import SuggestionCard from './SuggestionCard.vue'

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
</script>
