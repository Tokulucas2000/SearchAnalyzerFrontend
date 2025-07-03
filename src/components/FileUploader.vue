<template>
  <div class="file-uploader">
    <label for="csv-upload" class="upload-label">
      <span v-if="!file">Selecione o arquivo CSV</span>
      <span v-else>File selected: {{ file.name }}</span>
      <input 
        id="csv-upload" 
        type="file" 
        accept=".csv" 
        @change="handleFileChange" 
        class="file-input"
      />
    </label>
    <p v-if="error" class="error-message">{{ error }}</p>
  </div>
</template>

<script>
import { ref } from 'vue'

export default {
  emits: ['file-uploaded'],
  setup(props, { emit }) {
    const file = ref(null)
    const error = ref(null)

    const handleFileChange = (event) => {
      error.value = null
      const selectedFile = event.target.files[0]
      
      if (!selectedFile) {
        return
      }

      if (!selectedFile.name.endsWith('.csv')) {
        error.value = 'Please upload a CSV file'
        return
      }

      file.value = selectedFile
      emit('file-uploaded', selectedFile)
    }

    return {
      file,
      error,
      handleFileChange
    }
  }
}
</script>

<style>
.file-uploader {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.upload-label {
  display: inline-block;
  padding: 10px 15px;
  background-color: #42b983;
  color: white;
  border-radius: 4px;
  cursor: pointer;
  text-align: center;
  transition: background-color 0.3s;
}

.upload-label:hover {
  background-color: #3aa876;
}

.file-input {
  display: none;
}

.error-message {
  color: #e74c3c;
  margin: 0;
}
</style>