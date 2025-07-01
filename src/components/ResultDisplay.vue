<template>
  <div class="result-display">
    <div v-if="resultType === 'text'" class="text-result">
      <div v-html="formatText(result)"></div>
    </div>

    <div v-else-if="resultType === 'json'" class="json-result">
      <div class="json-preview">
        <pre>{{ prettyJson }}</pre>
      </div>
      <button @click="downloadJson" class="download-button">
        Download JSON
      </button>
    </div>

    <div v-else-if="resultType === 'html'" class="html-result">
      <div v-html="result"></div>
    </div>
  </div>
</template>

<script>
import { computed } from 'vue'

export default {
  props: {
    result: [String, Object],
    resultType: String
  },
  setup(props) {
    const prettyJson = computed(() => {
      if (props.resultType !== 'json') return ''
      try {        
        return JSON.stringify(JSON.parse(props.result))
      } catch {        
        return JSON.stringify(props.result)
      }
    })

    const formatText = (text) => {
      if (!text) return ''
      return text.replace(/\*\*(.*?)\*\*/g, '<strong>$1</strong>')
    }

    const downloadJson = () => {
      if (props.resultType !== 'json') return
      
      const dataStr = JSON.stringify(props.result, null, 2)
      const dataUri = 'data:application/json;charset=utf-8,' + encodeURIComponent(dataStr)
      
      const exportFileDefaultName = 'result.json'
      const linkElement = document.createElement('a')
      linkElement.setAttribute('href', dataUri)
      linkElement.setAttribute('download', exportFileDefaultName)
      linkElement.click()
    }

    return {
      prettyJson,
      formatText,
      downloadJson
    }
  }
}
</script>

<style>
.result-display {
  margin-top: 20px;
  padding: 20px;
  background-color: #f9f9f9;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.text-result {
  white-space: pre-wrap;
}

.json-preview {
  background-color: #282c34;
  color: #abb2bf;
  padding: 15px;
  border-radius: 4px;
  margin-bottom: 15px;
  max-height: 400px;
  overflow-y: auto;
}

.json-preview pre {
  margin: 0;
  font-family: 'Courier New', Courier, monospace;
}

.download-button {
  padding: 8px 15px;
  background-color: #42b983;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.download-button:hover {
  background-color: #3aa876;
}

.html-result {
  border: 1px solid #ddd;
  padding: 15px;
  border-radius: 4px;
}
</style>