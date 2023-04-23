<script setup>
import { ref } from "vue";

const text = ref("");

let rec;
if (!("webkitSpeechRecognition" in window)) {
  alert("disculpas, no puedes usar la API");
} else {
  rec = new webkitSpeechRecognition();
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
  rec.onresult = event => {
    iniciar(event);
  };
}

rec.start();
</script>

<template>
  <div>
    {{ text }}
  </div>
</template>

<style scoped>
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
