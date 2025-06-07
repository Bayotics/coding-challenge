<template>
  <div class="app-container">
    <header class="bg-primary text-white p-4 mb-4">
      <div class="container">
        <h1 class="mb-0 text-3xl text-black">AI Blog Assistant</h1>
      </div>
    </header>
    <main class="container">
      <div class="row">
        <div class="col-lg-8 mb-4">
          <BlogEditor />
        </div>
        <div class="col-lg-4">
          <AISuggestionPanel/>
          <DraftManager />
        </div>
      </div>
    </main>
    <DraftModal />
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import BlogEditor from './components/BlogEditor.vue'
import AISuggestionPanel from './components/AISuggestionPanel.vue'
import DraftManager from './components/DraftManager.vue'
import DraftModal from './components/DraftModal.vue'

// Initialize application
onMounted(() => {
  console.log("AI Blog Assistant initialized!")
})
</script>
<style>
</style>
