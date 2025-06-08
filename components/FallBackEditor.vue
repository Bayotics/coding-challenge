<template>
  <div class="fallback-editor">
    <div class="border-bottom p-2 bg-light">
      <div class="d-flex gap-1">
        <button type="button" class="btn btn-sm btn-light" @click="formatText('bold')" title="Bold">
          <strong>B</strong>
        </button>
        <button type="button" class="btn btn-sm btn-light" @click="formatText('italic')" title="Italic">
          <em>I</em>
        </button>
        <button type="button" class="btn btn-sm btn-light" @click="formatText('underline')" title="Underline">
          <u>U</u>
        </button>
        <button type="button" class="btn btn-sm btn-light" @click="formatText('insertUnorderedList')" title="Bullet List">
          •
        </button>
        <button type="button" class="btn btn-sm btn-light" @click="formatText('insertOrderedList')" title="Numbered List">
          1.
        </button>
      </div>
    </div>
    
    <div 
      ref="editorRef"
      class="p-3"
      contenteditable="true"
      @input="handleInput"
      @paste="handlePaste"
      v-html="content"
      style="min-height: 250px; outline: none;"
    ></div>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue'

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
