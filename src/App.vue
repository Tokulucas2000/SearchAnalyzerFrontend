<template>
  <div class="container">
    <h1>GPT CSV Processor</h1>
    <div class="app-container">
      <FileUploader @file-uploaded="handleFileUpload" />
      <ApiSettings ref="apiSettings" @process-request="processRequest" :is-loading="isLoading" />
      <ResultDisplay :result="result" :result-type="selectedType" v-if="result" />
    </div>
  </div>
</template>

<script>
import { ref } from 'vue'
import FileUploader from './components/FileUploader.vue'
import ApiSettings from './components/ApiSettings.vue'
import ResultDisplay from './components/ResultDisplay.vue'

export default {
  components: { FileUploader, ApiSettings, ResultDisplay },
  setup() {
    const selectedFile = ref(null)
    const selectedType = ref('text')
    const result = ref(null)
    const isLoading = ref(false)
    const apiSettings = ref(null)

    const handleFileUpload = (file) => {
      selectedFile.value = file
    }

    const processRequest = async (settings) => {
      isLoading.value = true
      result.value = null
      selectedType.value = settings.outputType

      try {
        const formData = new FormData()
        formData.append('file', selectedFile.value)
        formData.append('format', settings.outputType)
        formData.append('gptModel', settings.model)
        formData.append('gptToken', settings.apiKey)
        formData.append('description', settings.description)

        const response = await fetch(settings.apiUrl, {
          method: 'POST',
          body: formData,
          mode: 'cors',
          headers: {
            'Accept': 'application/json',
          },
        })
        console.log(response)
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`)
        }

        const contentType = response.headers.get('content-type')
        if (contentType && contentType.includes('application/json')) {
          result.value = await response.json()
        } else {
          result.value = await response.text()
        }
      } catch (error) {
        result.value = `Error: ${error.message}`
        console.error('Request failed:', error)
      } finally {
        isLoading.value = false
      }
    }

    return {
      selectedFile,
      selectedType,
      result,
      isLoading,
      apiSettings,
      handleFileUpload,
      processRequest
    }
  }
}
</script>

<style>
.container {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
  font-family: Arial, sans-serif;
}

.app-container {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

h1 {
  color: #2c3e50;
  text-align: center;
  margin-bottom: 30px;
}
</style>