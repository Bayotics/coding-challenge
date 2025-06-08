<template>
  <div class="card">
    <div class="card-body">
      <div class="mb-3">
        <label for="blogTitle" class="form-label">Title</label>
        <input 
          type="text" 
          class="form-control" 
          id="blogTitle" 
          :value="blogPost.title"
          @input="updateTitle"
          placeholder="Enter blog title"
        >
      </div>
      
      <div class="mb-3">
        <label class="form-label">Content</label>
        <div class="border rounded">
          <ClientOnlyQuillEditor 
            :content="blogPost.content"
            @update:content="updateContent"
          />
        </div>
      </div>
      
      <div>
        <label for="blogTags" class="form-label">Tags</label>
        <input 
          type="text" 
          class="form-control" 
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
