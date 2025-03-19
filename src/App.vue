<template>
  <div class="min-h-screen bg-gray-50 py-12 px-4 sm:px-6 lg:px-8">
    <div class="max-w-3xl mx-auto">
      <h1 class="text-4xl font-bold text-center text-gray-900 mb-8">
        Image Resizer
      </h1>

      <!-- Upload Section -->
      <div class="mb-8 bg-white rounded-lg shadow-md p-6">
        <label class="block text-sm font-medium text-gray-700 mb-4">
          Upload Gambar
        </label>
        <div class="mt-1 flex justify-center px-6 pt-5 pb-6 border-2 border-gray-300 border-dashed rounded-md">
          <div class="space-y-1 text-center">
            <svg class="mx-auto h-12 w-12 text-gray-400" stroke="currentColor" fill="none" viewBox="0 0 48 48">
              <path d="M28 8H12a4 4 0 00-4 4v20m32-12v8m0 0v8a4 4 0 01-4 4H12a4 4 0 01-4-4v-4m32-4l-3.172-3.172a4 4 0 00-5.656 0L28 28M8 32l9.172-9.172a4 4 0 015.656 0L28 28m0 0l4 4m4-24h8m-4-4v8m-12 4h.02" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
            </svg>
            <div class="flex text-sm text-gray-600">
              <label class="relative cursor-pointer bg-white rounded-md font-medium text-indigo-600 hover:text-indigo-500 focus-within:outline-none focus-within:ring-2 focus-within:ring-offset-2 focus-within:ring-indigo-500">
                <span>Upload file</span>
                <input 
                  type="file" 
                  class="sr-only"
                  accept="image/*"
                  @change="handleImageUpload"
                >
              </label>
              <p class="pl-1">atau drag and drop</p>
            </div>
            <p class="text-xs text-gray-500">PNG, JPG, GIF hingga 10MB</p>
          </div>
        </div>
      </div>

      <!-- Controls -->
      <div class="bg-white rounded-lg shadow-md p-6 mb-8" v-if="imageSrc">
        <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-2">Width</label>
            <div class="flex items-center space-x-4">
              <input 
                type="range" 
                min="100" 
                max="2000" 
                v-model.number="width"
                class="w-full h-2 bg-gray-200 rounded-lg appearance-none cursor-pointer"
              >
              <input 
                type="number" 
                v-model.number="width"
                class="w-20 px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500"
              >
            </div>
          </div>

          <div>
            <label class="block text-sm font-medium text-gray-700 mb-2">Height</label>
            <div class="flex items-center space-x-4">
              <input 
                type="range" 
                min="100" 
                max="2000" 
                v-model.number="height"
                class="w-full h-2 bg-gray-200 rounded-lg appearance-none cursor-pointer"
              >
              <input 
                type="number" 
                v-model.number="height"
                class="w-20 px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500"
              >
            </div>
          </div>
        </div>

        <div class="mt-6 grid grid-cols-1 md:grid-cols-2 gap-6">
          <div>
            <label class="flex items-center space-x-2">
              <input 
                type="checkbox" 
                v-model="lockAspectRatio"
                class="h-4 w-4 text-indigo-600 focus:ring-indigo-500 border-gray-300 rounded"
              >
              <span class="text-sm text-gray-700">Lock Aspect Ratio</span>
            </label>
          </div>

          <div>
            <label class="block text-sm font-medium text-gray-700 mb-2">Format</label>
            <select 
              v-model="selectedFormat"
              class="mt-1 block w-full pl-3 pr-10 py-2 text-base border-gray-300 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 rounded-md"
            >
              <option value="jpeg">JPEG</option>
              <option value="png">PNG</option>
              <option value="webp">WebP</option>
            </select>
          </div>
        </div>
      </div>

      <!-- Preview -->
      <div class="bg-white rounded-lg shadow-md p-6" v-if="imageSrc">
        <h2 class="text-lg font-medium text-gray-900 mb-4">Preview</h2>
        <div class="border-2 border-dashed border-gray-200 rounded-lg p-4 flex justify-center">
          <img 
            :src="imageSrc" 
            ref="previewImage"
            class="max-w-full h-auto"
            :style="{ width: `${width}px`, height: `${height}px` }"
            @load="handleImageLoad"
          >
        </div>
        
        <!-- Download Button -->
        <div class="mt-6 flex justify-center">
          <button 
            @click="downloadImage"
            class="inline-flex items-center px-6 py-3 border border-transparent text-base font-medium rounded-md shadow-sm text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
          >
            Download Resized Image
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue'

const imageSrc = ref(null)
const width = ref(800)
const height = ref(600)
const lockAspectRatio = ref(true)
const originalWidth = ref(0)
const originalHeight = ref(0)
const selectedFormat = ref('jpeg')
const previewImage = ref(null)

const handleImageUpload = (e) => {
  const file = e.target.files[0]
  if (file && file.type.startsWith('image/')) {
    const reader = new FileReader()
    reader.onload = (e) => {
      imageSrc.value = e.target.result
    }
    reader.readAsDataURL(file)
  }
}

const handleImageLoad = () => {
  originalWidth.value = previewImage.value.naturalWidth
  originalHeight.value = previewImage.value.naturalHeight
  if (lockAspectRatio.value) {
    width.value = previewImage.value.naturalWidth
    height.value = previewImage.value.naturalHeight
  }
}

watch([width, height], ([newWidth, newHeight], [oldWidth, oldHeight]) => {
  if (lockAspectRatio.value && imageSrc.value) {
    const aspectRatio = originalWidth.value / originalHeight.value
    
    if (newWidth !== oldWidth) {
      height.value = Math.round(newWidth / aspectRatio)
    } else if (newHeight !== oldHeight) {
      width.value = Math.round(newHeight * aspectRatio)
    }
  }
})

const downloadImage = () => {
  const canvas = document.createElement('canvas')
  const ctx = canvas.getContext('2d')
  
  canvas.width = width.value
  canvas.height = height.value
  
  ctx.drawImage(previewImage.value, 0, 0, width.value, height.value)
  
  canvas.toBlob(blob => {
    const link = document.createElement('a')
    link.download = `resized-image.${selectedFormat.value}`
    link.href = URL.createObjectURL(blob)
    link.click()
    URL.revokeObjectURL(link.href)
  }, `image/${selectedFormat.value}`, 0.9)
}
</script>