<script setup>
import { ref, watch } from "vue";

const text = ref("");

let rec;
if (!("webkitSpeechRecognition" in window)) {
  alert("disculpas, no puedes usar la API");
} else {
  window.SpeechRecognition =
    window.webkitSpeechRecognition || window.SpeechRecognition;
  rec = new window.SpeechRecognition();
  rec.lang = "es-AR";
  rec.continuous = true;
  rec.interim = true;
  /* rec.interimResults = true; */
  rec.onresult = event => {
    iniciar(event);
  };
}

function addCreateObject(obj) {
  let data = {
    name: obj,
    longitud: "",
    latitud: "",
  };
  if (navigator.geolocation) {
    var success = function (position) {
      console.log(position);
      (data.latitud = position.coords.latitude),
        (data.longitud = position.coords.longitude);
      text.value = data;
    };
    navigator.geolocation.getCurrentPosition(success, function (msg) {
      console.error(msg);
    });
  }
}

function iniciar(event) {
  for (let i = event.resultIndex; i < event.results.length; i++) {
    text.value = event.results[i][0].transcript;
    if (event.results[i][0].transcript.includes("guardar")) {
      if (event.results[i][0].transcript.split("guardar")[1]) {
        addCreateObject(event.results[i][0].transcript.split("guardar")[1]);
      }
    }
  }
  let navegador = navigator.userAgent;
  if (
    navigator.userAgent.match(/Android/i) ||
    navigator.userAgent.match(/webOS/i) ||
    navigator.userAgent.match(/iPhone/i) ||
    navigator.userAgent.match(/iPad/i) ||
    navigator.userAgent.match(/iPod/i) ||
    navigator.userAgent.match(/BlackBerry/i) ||
    navigator.userAgent.match(/Windows Phone/i)
  ) {
    alert("volver");
  } else {
    console.log("No estás usando un móvil");
  }
}

function test(){
  rec.start()
}
</script>

<template>
  <div @click="test" class="container">
    {{ text }}
  </div>
</template>

<style scoped>
.container {
  width: 100%;
  height: 90vh;
  display: flex;
  align-items: center;
  justify-content: center;
}
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
