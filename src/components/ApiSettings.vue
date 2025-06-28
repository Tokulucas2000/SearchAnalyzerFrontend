<template>
  <div class="api-settings">
    <div class="settings-form">
      <div class="form-group">
        <label for="output-type">Tipo de Saída:</label>
        <select id="output-type" v-model="outputType" class="form-control">
          <option value="text">Text</option>
          <option value="json">JSON</option>
          <option value="html">HTML</option>
        </select>
      </div>
      <div class="form-group">
        <label for="description">Descrição:</label>
        <textarea
          id="description"
          v-model="description"
          @input="adjustTextareaHeight"
          class="auto-resize-textarea"
          placeholder="Insira uma descrição para o processamento"
          rows="1"
        ></textarea>
      </div>
      <div class="form-group">
        <label for="model">Modelo GPT:</label>
        <select id="model" v-model="model" class="form-control">
          <option value="mini">Mini</option>
          <option value="nano">Nano</option>
        </select>
      </div>

      <div class="form-group">
        <label for="api-key">API Key:</label>
        <input 
          id="api-key" 
          type="password" 
          v-model="apiKey" 
          class="form-control" 
          placeholder="Insira a chave GPT API"
        />
      </div>

      <div class="form-group">
        <label for="api-url">API URL:</label>
        <input 
          id="api-url" 
          type="text" 
          v-model="apiUrl" 
          class="form-control" 
          placeholder="Insira a URL do endpoint API"
        />
      </div>

      <button 
        @click="handleSubmit" 
        :disabled="isLoading" 
        class="submit-button"
      >
        <span v-if="!isLoading">Process File</span>
        <span v-else class="loader"></span>
      </button>
    </div>
  </div>
</template>

<script>
import { ref, onMounted  } from 'vue'

export default {
  props: {
    isLoading: Boolean
  },
  emits: ['process-request'],
  setup(props, { emit }) {
    const outputType = ref('text')
    const model = ref('mini')
    const apiKey = ref('')
    const apiUrl = ref('')
    const description = ref('')

    const adjustTextareaHeight = (event) => {
      const textarea = event.target
      textarea.style.height = 'auto'
      textarea.style.height = `${textarea.scrollHeight}px`
    }

    onMounted(() => {
      if (description.value) {
        const textarea = document.querySelector('.auto-resize-textarea')
        if (textarea) {
          textarea.style.height = `${textarea.scrollHeight}px`
        }
      }
    })

    const handleSubmit = () => {
      if (!apiUrl.value) {
        alert('Please enter API URL')
        return
      }

      emit('process-request', {
        outputType: outputType.value,
        model: model.value,
        apiKey: apiKey.value,
        apiUrl: apiUrl.value,
        description: description.value
      })
    }

    return {
      outputType,
      model,
      apiKey,
      apiUrl,
      handleSubmit,
      description,
      adjustTextareaHeight,
    }
  }
}
</script>

<style>
.auto-resize-textarea {
  width: 100%;
  padding: 8px 12px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 16px;
  resize: none;
  overflow-y: hidden;
  min-height: 40px;
  transition: height 0.2s;
}

.api-settings {
  background-color: #f9f9f9;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.settings-form {
  display: flex;
  flex-direction: column;
  gap: 15px;
}

.form-group {
  display: flex;
  flex-direction: column;
  gap: 5px;
}

.form-control {
  padding: 8px 12px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 16px;
}

.submit-button {
  padding: 10px 15px;
  background-color: #2c3e50;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 16px;
  transition: background-color 0.3s;
  height: 40px;
}

.submit-button:hover:not(:disabled) {
  background-color: #1a252f;
}

.submit-button:disabled {
  background-color: #95a5a6;
  cursor: not-allowed;
}

.loader {
  display: inline-block;
  width: 20px;
  height: 20px;
  border: 3px solid rgba(255, 255, 255, 0.3);
  border-radius: 50%;
  border-top-color: white;
  animation: spin 1s ease-in-out infinite;
}

@keyframes spin {
  to { transform: rotate(360deg); }
}
</style>