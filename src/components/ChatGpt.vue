<script setup>
import { ref } from 'vue'

const eingabe = ref('')
const messages = ref([])
const laedt = ref(false)

const sendeNachricht = async () => {
    const text = eingabe.value.trim()
    if (!text) return

    messages.value.push({ role: 'user', content: text })
    eingabe.value = ''
    laedt.value = true

    try {
        const response = await fetch('https://bc30-176-7-192-170.ngrok-free.app/api/chat', {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({ messages: messages.value }),
        })

        if (!response.ok) throw new Error('Fehler bei API-Anfrage')

        const data = await response.json()
        messages.value.push(data.message)
    } catch (err) {
        console.error('Fehler:', err)
        messages.value.push({
            role: 'assistant',
            content: 'Es ist ein Fehler aufgetreten. Versuche es erneut.',
        })
    } finally {
        laedt.value = false
    }
}
</script>

<template>
    <div class="chat-container">
        <div class="messages">
            <div v-for="(msg, index) in messages" :key="index" :class="msg.role">
                <strong>{{ msg.role === 'user' ? 'Du' : 'Bot' }}:</strong>
                {{ msg.content }}
            </div>
        </div>
        <form @submit.prevent="sendeNachricht">
            <input v-model="eingabe" placeholder="Schreibe etwas..." :disabled="laedt" />
            <button type="submit" :disabled="laedt">Senden</button>
        </form>
    </div>
</template>

<style>
.chat-container {
    max-width: 600px;
    margin: auto;
    padding: 1rem;
}

.messages {
    margin-bottom: 1rem;
    max-height: 400px;
    overflow-y: auto;
    background: #f8f8f8;
    padding: 1rem;
    border-radius: 8px;
}

.user {
    text-align: right;
    color: blue;
}

.assistant {
    text-align: left;
    color: green;
}

input {
    width: 70%;
    padding: 0.5rem;
}

button {
    padding: 0.5rem;
    margin-left: 0.5rem;
}
</style>