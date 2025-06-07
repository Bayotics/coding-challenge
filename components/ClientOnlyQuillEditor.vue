<template>
  <div>
    <div v-if="!isClient && !loadingFailed" class="editor-placeholder">
      <div class="d-flex align-items-center justify-content-center" style="height: 300px;">
        <div class="text-muted">
          <div class="spinner-border spinner-border-sm me-2" role="status">
            <span class="visually-hidden">Loading...</span>
          </div>
          Loading editor...
        </div>
      </div>
    </div>
    
    <QuillEditor 
      v-if="isClient && !loadingFailed"
      :content="content"
      @update:content="$emit('update:content', $event)"
      contentType="html" 
      theme="snow"
      :toolbar="toolbarOptions"
      class="editor"
    />
    
    <FallbackEditor
      v-if="loadingFailed"
      :content="content"
      @update:content="$emit('update:content', $event)"
    />
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import FallbackEditor from './FallbackEditor.vue'

// Dynamic import to avoid SSR issues
let QuillEditor = null

const props = defineProps({
  content: {
    type: String,
    default: ''
  }
})

const emit = defineEmits(['update:content'])

const isClient = ref(false)
const loadingFailed = ref(false)

const toolbarOptions = [
  ['bold', 'italic', 'underline', 'strike'],
  ['blockquote', 'code-block'],
  [{ 'header': 1 }, { 'header': 2 }],
  [{ 'list': 'ordered'}, { 'list': 'bullet' }],
  [{ 'script': 'sub'}, { 'script': 'super' }],
  [{ 'indent': '-1'}, { 'indent': '+1' }],
  [{ 'direction': 'rtl' }],
  [{ 'size': ['small', false, 'large', 'huge'] }],
  [{ 'header': [1, 2, 3, 4, 5, 6, false] }],
  [{ 'color': [] }, { 'background': [] }],
  [{ 'font': [] }],
  [{ 'align': [] }],
  ['clean'],
  ['link', 'image']
]

onMounted(async () => {
  try {
    // Dynamic import of Quill editor to avoid SSR issues
    const { QuillEditor: QE } = await import('@vueup/vue-quill')
    await import('@vueup/vue-quill/dist/vue-quill.snow.css')
    
    QuillEditor = QE
    isClient.value = true
  } catch (error) {
    console.error('Failed to load Quill editor, using fallback:', error)
    loadingFailed.value = true
  }
})
</script>

<style scoped>
.editor {
  height: 300px;
}

.editor-placeholder {
  border: 1px solid #dee2e6;
  border-radius: 0.375rem;
  background-color: #f8f9fa;
}
</style>
