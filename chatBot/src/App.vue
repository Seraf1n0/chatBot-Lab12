<script setup lang="ts">
import { ref } from 'vue';
import baseConocimiento from '../data/baseConocimiento.json';
import Fuse from 'fuse.js'
import { enviarMensaje as enviarMensajeAPI } from '@/services/apiCliente'
import type { BodyChat } from '@/interfaces/BodyChat'

const mensajeUsuario = ref("")
const respuestaBot = ref("")

// Esto es para buscar las coincidencias 
// https://www.hablemosdefrontend.dev/post/busqueda-difusa-en-cliente-con-fusejs/
const fuse = new Fuse(Object.keys(baseConocimiento), {
  includeScore: true,
  threshold: 0.4,
});

const enviarMensaje = async () => {
  
  try {
    console.log("Mensaje del usuario:", mensajeUsuario.value);
    const busqueda = fuse.search(mensajeUsuario.value);

    if (busqueda.length > 0 && busqueda[0]?.item) {
      const resultado = busqueda[0].item
      const respuesta = baseConocimiento[resultado as keyof typeof baseConocimiento];
      console.log("Respuesta de coincidencia:", respuesta)
// Aqui armo el body y le paso primero la base de conocimiento o sea el json
      const body: BodyChat = {
        model: "openai/gpt-oss-20b:free",
        messages: [
          {
            role: "system",
            content: `Eres un asistente del banco prometedores el cliente te esta preguntando esto ${mensajeUsuario.value} pero tu debes solo utilizar la informacion que te doy en ${respuesta} si ves que no hay respuesta adecuada entonces quiero que le digas al usuario algo como esto Esta información no está en el sistema 
del Banco XYZ, pero puedo ofrecerte una orientación general.`
          },
          {
            role: "user",
            content: mensajeUsuario.value
          }
        ]
      }

      const data = await enviarMensajeAPI(body)
      console.log("Respuesta del botardo:", data);
      respuestaBot.value = data.choices[0]?.message?.content || "No hubo respuesta arrarar"
      console.log("Mensaje del bot:", respuestaBot.value);
    } 
  } catch (error) { 
    console.error("Error pejesemar nos tiro la vara", error);
  }
};
</script>

<template>
  <h1>You did it!</h1>
  <p>
    Visit <a href="https://vuejs.org/" target="_blank" rel="noopener">vuejs.org</a> to read the
    documentation
  </p>
  <textarea v-model="mensajeUsuario"></textarea>
  <button @click="enviarMensaje">Send</button>
</template>

<style scoped></style>
