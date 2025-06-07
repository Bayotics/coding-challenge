<template>
  <div class="bg-white rounded-lg shadow-sm overflow-hidden">
    <div class="p-4 border-b border-gray-200">
      <div class="mb-4">
        <label for="blogTitle" class="block text-sm font-medium text-gray-700 mb-1">Title</label>
        <input 
          type="text" 
          class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent" 
          id="blogTitle" 
          :value="blogPost.title"
          @input="updateTitle"
          placeholder="Enter blog title"
        >
      </div>
      
      <div class="mb-4">
        <label class="block text-sm font-medium text-gray-700 mb-1">Content</label>
        <div class="border border-gray-300 rounded-md overflow-hidden">
          <ClientOnlyQuillEditor 
            :content="blogPost.content"
            @update:content="updateContent"
          />
        </div>
      </div>
      
      <div>
        <label for="blogTags" class="block text-sm font-medium text-gray-700 mb-1">Tags</label>
        <input 
          type="text" 
          class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent" 
          id="blogTags" 
          :value="blogPost.tags"
          @input="updateTags"
          placeholder="Enter tags separated by commas"
        >
      </div>
    </div>
  </div>
</template>

<script setup>
import ClientOnlyQuillEditor from './ClientOnlyQuillEditor.vue'

// Props and emits
const props = defineProps({
  blogPost: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['update:blogPost', 'saveDraft'])

// Update methods
const updateTitle = (event) => {
  emit('update:blogPost', { ...props.blogPost, title: event.target.value })
}

const updateContent = (content) => {
  emit('update:blogPost', { ...props.blogPost, content })
}

const updateTags = (event) => {
  emit('update:blogPost', { ...props.blogPost, tags: event.target.value })
}
</script>
