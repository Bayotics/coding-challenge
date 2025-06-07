<template>
  <div class="fallback-editor">
    <div class="editor-toolbar border-bottom p-2">
      <div class="btn-group btn-group-sm" role="group">
        <button type="button" class="btn btn-outline-secondary" @click="formatText('bold')" title="Bold">
          <strong>B</strong>
        </button>
        <button type="button" class="btn btn-outline-secondary" @click="formatText('italic')" title="Italic">
          <em>I</em>
        </button>
        <button type="button" class="btn btn-outline-secondary" @click="formatText('underline')" title="Underline">
          <u>U</u>
        </button>
      </div>
      <div class="btn-group btn-group-sm ms-2" role="group">
        <button type="button" class="btn btn-outline-secondary" @click="formatText('insertUnorderedList')" title="Bullet List">
          •
        </button>
        <button type="button" class="btn btn-outline-secondary" @click="formatText('insertOrderedList')" title="Numbered List">
          1.
        </button>
      </div>
    </div>
    
    <div 
      ref="editorRef"
      class="editor-content p-3"
      contenteditable="true"
      @input="handleInput"
      @paste="handlePaste"
      v-html="content"
      style="min-height: 250px; outline: none;"
    ></div>
  </div>
</template>

<script setup>
import { ref, watch, nextTick } from 'vue'

const props = defineProps({
  content: {
    type: String,
    default: ''
  }
})

const emit = defineEmits(['update:content'])

const editorRef = ref(null)

const formatText = (command) => {
  document.execCommand(command, false, null)
  editorRef.value.focus()
}

const handleInput = () => {
  const content = editorRef.value.innerHTML
  emit('update:content', content)
}

const handlePaste = (event) => {
  event.preventDefault()
  const text = event.clipboardData.getData('text/plain')
  document.execCommand('insertText', false, text)
}

watch(() => props.content, (newContent) => {
  if (editorRef.value && editorRef.value.innerHTML !== newContent) {
    editorRef.value.innerHTML = newContent
  }
})
</script>

<style scoped>
.fallback-editor {
  border: 1px solid #dee2e6;
  border-radius: 0.375rem;
  background-color: white;
}

.editor-toolbar {
  background-color: #f8f9fa;
}

.editor-content {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  line-height: 1.6;
}

.editor-content:focus {
  box-shadow: inset 0 0 0 2px rgba(13, 110, 253, 0.25);
}
</style>
