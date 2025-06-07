<template>
  <div class="card shadow-sm">
    <div class="card-header d-flex justify-content-between align-items-center">
      <h2 class="h5 mb-0">Blog Editor</h2>
      <div>
        <button class="btn btn-outline-secondary me-2" @click="$emit('saveDraft')">
          <SaveIcon class="me-1" size="16" />
          Save Draft
        </button>
        <button class="btn btn-outline-primary" @click="$emit('loadDrafts')">
          <FolderOpenIcon class="me-1" size="16" />
          Load Draft
        </button>
      </div>
    </div>
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
        <div class="editor-container border rounded">
          <ClientOnlyQuillEditor 
            :content="blogPost.content"
            @update:content="updateContent"
          />
        </div>
      </div>
      
      <div class="mb-3">
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
import { SaveIcon, FolderOpenIcon } from 'lucide-vue-next'
import ClientOnlyQuillEditor from './ClientOnlyQuillEditor.vue'

// Props and emits
const props = defineProps({
  blogPost: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['update:blogPost', 'saveDraft', 'loadDrafts'])

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

<style scoped>
.editor-container {
  min-height: 300px;
}
</style>
