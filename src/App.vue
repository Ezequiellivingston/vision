<script setup>
import { ref, watch } from "vue";
import alarma from "../public/alarma.mp3";

let snd = new Audio(alarma);

const text = ref("");
const save = ref([]);
const positionNew = ref({ latitud: "", longitud: "" });
const maxValor = ref(null);
const test = ref(0);

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

function initSeartch(positionObj) {
  snd.play()
  snd.loop = true;
  var success = function (position) {
    console.log(position);
    console.log(snd);

    positionNew.value.latitud = position.coords.latitude;
    positionNew.value.longitud = position.coords.longitude;

    let localActual = position.coords.latitude - position.coords.longitude;
    let objectoAbuscar = positionObj.latitud - positionObj.longitud;
    let distancia = localActual - objectoAbuscar == 0 ? 1 : localActual - objectoAbuscar;
    
    if (maxValor.value == null) {   
        maxValor.value = distancia;        
    } else {
    }



    let max = (distancia * 100) /  maxValor.value;
    test.value = (distancia * 100) /  maxValor.value;

    
    if (maxValor) {
      if(max / 100 > 1){
        snd.volume = 1
      }else if (max / 100 < 0) {
        snd.volume = 1
      } else {
        snd.volume = max / 100 
      }
    }
  };
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

</script>

<template>
 <!--  <div @click="mobile" class="container">
    {{ test / 100 }}
    {{ text }}
  </div> -->
  <div>
    <iframe src="https://youtube.com/" frameborder="0"></iframe>
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
