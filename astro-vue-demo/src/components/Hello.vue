<template>
  <div>
    <h2>Hello</h2>

    <form @submit.prevent="sendHello" style="display:flex;gap:8px;align-items:center;">
      <!-- <label for="name" class="sr-only">Name</label> -->
      <input
        id="name"
        v-model="name"
        type="text"
        placeholder="Ingresa tu nombre"
        aria-label="name"
        :disabled="loading"
        style="padding:6px;min-width:200px;"
      />
      <button type="submit" :disabled="loading || !name">
        {{ loading ? 'Enviando...' : 'Enviar' }}
      </button>
    </form>

    <pre id="hello-output" aria-live="polite" style="white-space:pre-wrap; margin-top:12px;">{{ output }}</pre>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const props = defineProps({
  apiBase: { type: String, default: '/' }
})

const name = ref('')
const loading = ref(false)
const output = ref('')

function buildUrl(path) {
  const base = (props.apiBase || '/').replace(/\/$/, '')
  return `${base}/${path.replace(/^\//, '')}`
}

async function sendHello() {
  loading.value = true
  output.value = ''
  try {
    const url = buildUrl('/hello')
    const res = await fetch(url, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ name: name.value }),
      cache: 'no-store'
    })

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