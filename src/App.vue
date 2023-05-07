<script setup>
import { computed, ref, watch } from "vue";
import alarma from "../public/alarma.mp3";

let snd = new Audio(alarma);

const text = ref("");
const save = ref([]);
const positionNew = ref({ latitud: "", longitud: "" });
const maxValor = ref(null);
const test = ref(0);
const coordenadas = ref();

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
      data.latitud = position.coords.latitude;
      data.longitud = position.coords.longitude;
      text.value = data;
      save.value.push(data);
    };
    navigator.geolocation.getCurrentPosition(success, function (msg) {
      console.error(msg);
    });
  }
}

function getDistanciaMetros(lat1, lon1, lat2, lon2) {
  let rad = function (x) {
    return (x * Math.PI) / 180;
  };
  var R = 6378.137; //Radio de la tierra en km
  var dLat = rad(lat2 - lat1);
  var dLong = rad(lon2 - lon1);
  var a =
    Math.sin(dLat / 2) * Math.sin(dLat / 2) +
    Math.cos(rad(lat1)) *
      Math.cos(rad(lat2)) *
      Math.sin(dLong / 2) *
      Math.sin(dLong / 2);
  var c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));

  //aquí obtienes la distancia en metros por la conversion 1Km =1000m
  var d = R * c * 1000;
  return d;
}

function initSeartch(positionObj) {
  console.log(positionObj);
  /* snd.play();
  snd.loop = true;
  snd.volume = 1 */

  var success = function (position) {
    let positionSearch = {
      lat: position.coords.latitude,
      lon: position.coords.longitude,
      lat1: positionObj.latitud,
      lon1: positionObj.longitud,
    };
    coordenadas.value = positionSearch;

    let distancia = getDistanciaMetros(
      positionSearch.lat,
      positionSearch.lon,
      positionSearch.lat1,
      positionSearch.lon1
    );
    let truncate = parseFloat(distancia).toFixed(2);
    speechSynthesis.speak(
      new SpeechSynthesisUtterance(`El objeto esta a ${truncate} metros`)
    );
    test.value = distancia;
  };
  console.log(navigator.geolocation);
  navigator.geolocation.watchPosition(success, function (msg) {
    console.error(msg);
  });
}

function searchObject(obj) {
  console.log(obj, "buscando", save);
  const data = save.value.find(e => {
    if (e.name.includes(obj)) {
      return e;
    }
  });
  initSeartch(data);
}

function iniciar(event) {
  for (let i = event.resultIndex; i < event.results.length; i++) {
    text.value = event.results[i][0].transcript.toLowerCase();
    let textSearch = event.results[i][0].transcript.toLowerCase();
    if (textSearch.includes("guardar")) {
      if (textSearch.split("guardar ")[1]) {
        addCreateObject(textSearch.split("guardar ")[1]);
      }
    }
    if (textSearch.includes("buscar")) {
      if (textSearch.split("buscar ")[1]) {
        searchObject(textSearch.split("buscar ")[1]);
      }
    }
  }
}

function mobile() {
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
    rec.start();
  } else {
    console.log("No estás usando un móvil");
  }
}

/* rec.start(); */

if ('nfc' in navigator) {
  // La API Web NFC es compatible con el navegador
  alert('si acepta')
  navigator.nfc.watch(function(message) {
    alert('asdasd')
  // Se ha detectado una etiqueta NFC
  // Aquí se puede procesar la etiqueta y hacer algo con los datos
});
} else {
  alert('no acepta')
  // La API Web NFC no es compatible con el navegador
}


</script>

<template>
  <div @click="mobile" class="container">
    {{ text.name }}
    {{ test }} metros
  </div>
  <div>
    {{ coordenadas }}
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
