<script setup lang="ts">
import { ref } from 'vue'
import MessageBubble from './MessageBubble.vue'
import type { Message } from '../interfaces/ChatBot'

const messages = ref<Message[]>([
  { id: '1', text: 'Hola! ¿En qué puedo ayudarte?', sender: 'bot', time: new Date().toLocaleTimeString() },
])

const inputText = ref('')

function sendMessage() {
  const text = inputText.value && inputText.value.trim()
  if (!text) return
  const userMsg: Message = {
    id: String(Date.now()) + Math.random().toString(36).slice(2),
    text,
    sender: 'user',
    time: new Date().toLocaleTimeString(),
  }
  messages.value.push(userMsg)
  inputText.value = ''
  setTimeout(() => {
    const botMsg: Message = {
      id: String(Date.now()) + Math.random().toString(36).slice(2),
      text: `Respuesta automática a: "${text}"`,
      sender: 'bot',
      time: new Date().toLocaleTimeString(),
    }
    messages.value.push(botMsg)
    const el = document.querySelector('.chatbot-messages') as HTMLElement | null
    if (el) el.scrollTop = el.scrollHeight
  }, 700)
}
</script>

<template>
  <div class="min-h-screen flex items-center justify-center p-4 bg-gradient-to-b from-slate-950 to-slate-900">
    <div
      class="w-full max-w-2xl h-[80vh] md:h-[70vh] flex flex-col rounded-2xl shadow-2xl overflow-hidden
             bg-gradient-to-b from-violet-900 via-violet-500 to-indigo-900 text-gray-100"
    >
      <header class="px-6 py-4 flex items-center justify-between border-b border-purple-700/60">
        <h2 class="text-xl font-semibold">ChatBot</h2>
        <span class="text-sm text-purple-300 hidden sm:inline">Modo oscuro</span>
      </header>

      <main class="flex-1 overflow-auto p-4 chatbot-messages flex flex-col gap-4">
        <MessageBubble
          v-for="(msg, i) in messages"
          :key="msg.id"
          :message="msg"
          :index="i"
        />
      </main>

      <footer class="px-4 py-3 bg-purple-950/40 border-t border-purple-800/60">
        <div class="flex gap-3">
          <input
            v-model="inputText"
            @keyup.enter="sendMessage"
            type="text"
            class="flex-1 rounded-lg bg-purple-900/60 placeholder:text-purple-300 px-4 py-2
                   focus:outline-none focus:ring-2 focus:ring-purple-500 transition"
            placeholder="Pregunta algo..."
            aria-label="Mensaje"
          />
          <button
            @click="sendMessage"
            class="px-4 py-2 rounded-lg bg-gradient-to-br from-fuchsia-600 to-purple-600
                   hover:opacity-95 active:scale-95 transition text-white"
          >
            Enviar
          </button>
        </div>
      </footer>
    </div>
  </div>
</template>

<style scoped></style>
