<template>
  <div class="bg-white rounded-lg shadow-sm overflow-hidden">
    <div class="bg-gray-50 px-4 py-3 border-b border-gray-200">
      <h2 class="text-sm font-medium text-gray-700">AI Suggestions</h2>
    </div>
    
    <!-- API Key Warning (only shown if API key is missing) -->
    <div v-if="!hasApiKey" class="p-4 bg-yellow-50 border-b border-yellow-100">
      <div class="flex items-start">
        <div class="flex-shrink-0">
          <AlertTriangleIcon class="h-5 w-5 text-yellow-400" />
        </div>
        <div class="ml-3">
          <h3 class="text-sm font-medium text-yellow-800">Missing API Key</h3>
          <div class="mt-1 text-sm text-yellow-700">
            <p>Please add your OpenAI API key to the .env file.</p>
            <p class="mt-1 text-xs">
              Add <code class="bg-yellow-100 px-1 py-0.5 rounded">VITE_OPENAI_API_KEY=your_api_key_here</code> to your .env file and restart the server.
            </p>
          </div>
        </div>
      </div>
    </div>

    <!-- AI Suggestion Buttons -->
    <div class="p-4">
      <div class="space-y-3">
        <button 
          class="w-full flex items-center justify-center px-4 py-2 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-blue-600 hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-blue-500 disabled:opacity-50 disabled:cursor-not-allowed"
          @click="generateTitle" 
          :disabled="isGenerating || !hasContent || !hasApiKey"
        >
          <SparklesIcon class="w-4 h-4 mr-2" />
          Generate Title
        </button>
        <button 
          class="w-full flex items-center justify-center px-4 py-2 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-blue-600 hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-blue-500 disabled:opacity-50 disabled:cursor-not-allowed"
          @click="generateSummary" 
          :disabled="isGenerating || !hasContent || !hasApiKey"
        >
          <FileTextIcon class="w-4 h-4 mr-2" />
          Summarize Content
        </button>
        <button 
          class="w-full flex items-center justify-center px-4 py-2 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-blue-600 hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-blue-500 disabled:opacity-50 disabled:cursor-not-allowed"
          @click="generateKeywords" 
          :disabled="isGenerating || !hasContent || !hasApiKey"
        >
          <TagIcon class="w-4 h-4 mr-2" />
          Suggest Keywords
        </button>
      </div>

      <div v-if="isGenerating" class="mt-4 flex justify-center">
        <div class="flex flex-col items-center">
          <div class="animate-spin rounded-full h-8 w-8 border-b-2 border-blue-600"></div>
          <p class="mt-2 text-sm text-gray-500">Generating suggestions...</p>
        </div>
      </div>

      <div v-if="currentSuggestion.content" class="mt-4">
        <div class="bg-gray-50 border border-gray-200 rounded-md overflow-hidden">
          <div class="bg-gray-100 px-4 py-2 border-b border-gray-200 flex justify-between items-center">
            <span class="text-sm font-medium text-gray-700">{{ currentSuggestion.type }}</span>
            <button 
              v-if="!isErrorMessage"
              @click="handleApplySuggestion"
              class="text-xs bg-blue-600 hover:bg-blue-700 text-white px-2 py-1 rounded"
            >
              Apply
            </button>
          </div>
          <div class="p-4">
            <p class="text-sm text-gray-800 whitespace-pre-wrap">{{ currentSuggestion.content }}</p>
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
         content.includes('failed') 
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
