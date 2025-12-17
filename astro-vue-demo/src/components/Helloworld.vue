<template>
  <div>
    <h2>Hello World</h2>
    <button @click="getHello" :disabled="loading">
      {{ loading ? 'Cargando...' : 'Get Hello World' }}
    </button>

    <pre id="hello-output" aria-live="polite" style="white-space:pre-wrap; margin-top:12px;">{{ output }}</pre>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const props = defineProps({
  apiBase: { type: String, default: '/' }
})

const loading = ref(false)
const output = ref('')

function buildUrl(path) {
  const base = (props.apiBase || '/').replace(/\/$/, '')
  return `${base}/${path.replace(/^\//, '')}`
}

async function getHello() {
  loading.value = true
  output.value = ''
  try {
    const res = await fetch(buildUrl('/helloworld'), { cache: 'no-store' })
    if (!res.ok) throw new Error(`HTTP ${res.status} ${res.statusText}`)

    const ct = res.headers.get('content-type') || ''
    if (ct.includes('application/json')) {
      const json = await res.json()
      output.value = JSON.stringify(json, null, 2)
    } else {
      output.value = await res.text()
    }
  } catch (err) {
    output.value = 'Error: ' + err.message
  } finally {
    loading.value = false
  }
}
</script>