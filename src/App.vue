<template>
  <main style="max-width:520px;margin:40px auto;font-family:system-ui">
    <h2 style="margin:0 0 8px">POC Email</h2>
    <p style="margin:0 0 18px;color:#555">
      Backend: <code>{{ base }}</code>
    </p>

    <form @submit.prevent="send">
      <label>Para</label>
      <input v-model="to" placeholder="destinatario" style="width:100%;margin:6px 0 12px;padding:8px;border:1px solid #ddd;border-radius:10px"/>

      <label>Asunto</label>
      <input v-model="subject" placeholder="asunto" style="width:100%;margin:6px 0 12px;padding:8px;border:1px solid #ddd;border-radius:10px"/>

      <label>Mensaje</label>
      <textarea v-model="text" rows="5" placeholder="mensaje"
        style="width:100%;margin:6px 0 12px;padding:8px;border:1px solid #ddd;border-radius:10px"></textarea>

      <button :disabled="loading" style="padding:10px 16px;border-radius:10px;border:1px solid #ddd">
        {{ loading ? 'Enviando…' : 'Enviar' }}
      </button>
      <span v-if="msg" :style="msgOk ? 'color:green;margin-left:10px' : 'color:#b00;margin-left:10px'">
        {{ msg }}
      </span>
    </form>

    <hr style="margin:24px 0;opacity:.2">

    <div>
      <button @click="checkHealth" style="padding:8px 12px;border:1px solid #ddd;border-radius:10px">
        Probar /health
      </button>
      <span v-if="healthMsg" style="margin-left:10px">{{ healthMsg }}</span>
    </div>
  </main>
</template>

<script setup>
import { ref } from 'vue'
const base = import.meta.env.VITE_API_BASE

const to = ref('mark-tec-v1@outlook.com')
const subject = ref('Hola desde la POC')
const text = ref('Funciona 🔥')
const loading = ref(false)
const msg = ref('')
const msgOk = ref(false)
const healthMsg = ref('')

const send = async () => {
  loading.value = true; msg.value = ''; msgOk.value = false
  try {
  console.log("BAGP",`https://kz-media-backend-python-production.up.railway.app/email`)
    const res = await fetch(`https://kz-media-backend-python-production.up.railway.app/email`, {
      method: 'POST',
      headers: {'Content-Type':'application/json'},
      body: JSON.stringify({ to: to.value, subject: subject.value, text: text.value })
    })
    msgOk.value = res.ok
    msg.value = res.ok ? 'Enviado ✅' : `Error ❌ (${res.status})`
  } catch (e) {
    msgOk.value = false
    msg.value = 'Error de red ❌'
  } finally {
    loading.value = false
  }
}

const checkHealth = async () => {
  healthMsg.value = '...'
  try {
      console.log("BAGP",`https://kz-media-backend-python-production.up.railway.app/health`)
    const res = await fetch(`https://kz-media-backend-python-production.up.railway.app/health`)
    healthMsg.value = res.ok ? 'OK ✅' : `Error (${res.status})`
  } catch {
    healthMsg.value = 'No responde ❌'
  }
}
</script>
