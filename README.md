navigator.geolocation.watchPosition(
  (position) => {
    const latitude = position.coords.latitude;
    const longitude = position.coords.longitude;
    const precisao = position.coords.accuracy;

    console.log("Latitude:", latitude);
    console.log("Longitude:", longitude);
    console.log("Precisão:", precisao, "metros");
  },
  (error) => {
    console.error("Erro GPS:", error.message);
  },
  {
    enableHighAccuracy: true,
    maximumAge: 0,
    timeout: 20000
  }
);navigator.geolocation.watchPosition(
  (position) => {
    const latitude = position.coords.latitude;
    const longitude = position.coords.longitude;
    const precisao = position.coords.accuracy;

    console.log("Latitude:", latitude);
    console.log("Longitude:", longitude);
    console.log("Precisão:", precisao, "metros");
  },
  (error) => {
    console.error("Erro GPS:", error.message);
  },
  {
    enableHighAccuracy: true,
    maximumAge: 0,
    timeout: 20000
  }
);
<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<meta name="theme-color" content="#071522">
<title>GeoMeasure Flight 2.0</title>

<link rel="stylesheet"
href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css">

<style>
:root {
  --bg:#071522;
  --panel:#102638;
  --panel2:#153247;
  --line:#29485d;
  --cyan:#36d9f5;
  --green:#31d99a;
  --yellow:#f5c65b;
  --red:#ff6575;
  --text:#eaf7ff;
  --muted:#91aaba;
}

* { box-sizing:border-box; }

body {
  margin:0;
  background:var(--bg);
  color:var(--text);
  font-family:Arial,Helvetica,sans-serif;
}

main {
  max-width:1100px;
  margin:auto;
  padding:14px;
}

header {
  display:flex;
  align-items:center;
  justify-content:space-between;
  gap:10px;
  margin-bottom:15px;
}

h1 {
  font-size:21px;
  margin:0;
  letter-spacing:1px;
}

.subtitle {
  font-size:11px;
  color:var(--muted);
  margin-top:5px;
  letter-spacing:1px;
}

.status {
  background:#352c16;
  color:var(--yellow);
  border:1px solid #78612a;
  border-radius:30px;
  padding:7px 11px;
  font-size:11px;
  font-weight:bold;
}

.section-title {
  color:var(--cyan);
  font-size:11px;
  font-weight:bold;
  letter-spacing:2px;
  margin:18px 2px 9px;
}

#map {
  height:420px;
  min-height:300px;
  border-radius:14px;
  border:1px solid var(--line);
  overflow:hidden;
  z-index:0;
}

.metrics {
  display:grid;
  grid-template-columns:repeat(2,1fr);
  gap:9px;
  margin-top:10px;
}

.card,.panel {
  background:linear-gradient(145deg,var(--panel2),var(--panel));
  border:1px solid var(--line);
  border-radius:12px;
  padding:13px;
}

.card small {
  color:var(--muted);
  display:block;
  font-size:11px;
  margin-bottom:7px;
}

.card strong {
  font-size:23px;
  font-variant-numeric:tabular-nums;
}

.unit {
  font-size:11px;
  color:var(--muted);
}

.grid {
  display:grid;
  grid-template-columns:1fr 1fr;
  gap:9px;
}

.panel {
  margin-top:10px;
}

.panel-title {
  color:var(--cyan);
  font-size:11px;
  letter-spacing:1.5px;
  margin-bottom:12px;
  font-weight:bold;
}

.compass-area {
  display:flex;
  align-items:center;
  justify-content:center;
  gap:22px;
  min-height:130px;
}

.compass {
  width:105px;
  height:105px;
  border:2px solid #54778b;
  border-radius:50%;
  background:#0a1c2a;
  position:relative;
  display:flex;
  justify-content:center;
  align-items:center;
}

.compass:before {
  content:"N";
  position:absolute;
  top:5px;
  color:var(--yellow);
  font-weight:bold;
  font-size:12px;
}

.compass-needle {
  width:4px;
  height:68px;
  position:absolute;
  top:17px;
  left:49px;
  transform-origin:center center;
  transform:rotate(0deg);
}

.compass-needle:before {
  content:"";
  position:absolute;
  top:0;
  left:-4px;
  border-left:6px solid transparent;
  border-right:6px solid transparent;
  border-bottom:33px solid var(--cyan);
}

.compass-needle:after {
  content:"";
  position:absolute;
  top:34px;
  left:-4px;
  border-left:6px solid transparent;
  border-right:6px solid transparent;
  border-top:29px solid #718b9b;
}

.compass-info strong {
  font-size:25px;
}

.readout {
  display:flex;
  justify-content:space-between;
  padding:7px 0;
  border-bottom:1px solid var(--line);
  font-size:13px;
  gap:10px;
}

.readout:last-child {
  border-bottom:0;
}

.buttons {
  display:grid;
  grid-template-columns:repeat(2,1fr);
  gap:8px;
  margin-top:10px;
}

button {
  background:#18364b;
  color:var(--text);
  border:1px solid var(--line);
  border-radius:10px;
  padding:13px 8px;
  font-weight:bold;
  font-size:13px;
  cursor:pointer;
}

button:active {
  transform:scale(.98);
}

button:disabled {
  opacity:.4;
  cursor:not-allowed;
}

.primary {
  background:#087f9b;
  border-color:#19b9d6;
}

.green {
  background:#08764e;
  border-color:#20bd84;
}

.warning {
  background:#493715;
  border-color:#826421;
}

.danger {
  background:#52212b;
  border-color:#a63c4c;
}

.muted {
  color:var(--muted);
  font-size:11px;
  line-height:1.6;
}

#message {
  color:#b9d9e8;
  font-size:13px;
  line-height:1.5;
  margin-top:10px;
}

#history {
  font-size:12px;
  color:var(--muted);
  line-height:1.8;
}

@media(min-width:650px) {
  .metrics {
    grid-template-columns:repeat(4,1fr);
  }
  .buttons {
    grid-template-columns:repeat(4,1fr);
  }
  .grid {
    grid-template-columns:1fr 1fr;
  }
}
</style>
</head>

<body>
<main>

<header>
  <div>
    <h1>✈ GEOMEASURE FLIGHT</h1>
    <div class="subtitle">GPS CONTROL SYSTEM • VERSION 2.0</div>
  </div>
  <span id="gpsStatus" class="status">GPS EM ESPERA</span>
</header>

<div class="section-title">01 / MAPA DE NAVEGAÇÃO</div>

<div id="map"></div>

<div class="metrics">

  <div class="card">
    <small>VELOCIDADE GPS</small>
    <strong id="speed">—</strong>
    <span class="unit">km/h</span>
  </div>

  <div class="card">
    <small>ALTITUDE GPS</small>
    <strong id="altitude">—</strong>
    <span class="unit">m</span>
  </div>

  <div class="card">
    <small>DISTÂNCIA PERCORRIDA</small>
    <strong id="distance">0</strong>
    <span class="unit">m</span>
  </div>

  <div class="card">
    <small>PRECISÃO GPS</small>
    <strong id="accuracy">—</strong>
    <span class="unit">m</span>
  </div>

</div>

<div class="grid">

  <section class="panel">
    <div class="panel-title">02 / BÚSSOLA</div>

    <div class="compass-area">
      <div class="compass">
        <div id="needle" class="compass-needle"></div>
      </div>

      <div class="compass-info">
        <div class="muted">DIREÇÃO</div>
        <strong id="heading">—°</strong>
        <div id="direction" class="muted">
          Aguardando sinal
        </div>
      </div>
    </div>

    <button id="compassButton" style="width:100%">
      Ativar bússola do aparelho
    </button>
  </section>

  <section class="panel">
    <div class="panel-title">03 / POSIÇÃO DO PILOTO</div>

    <div class="readout">
      <span>Latitude</span>
      <strong id="lat">—</strong>
    </div>

    <div class="readout">
      <span>Longitude</span>
      <strong id="lng">—</strong>
    </div>

    <div class="readout">
      <span>Distância do início</span>
      <strong id="homeDistance">—</strong>
    </div>

    <div class="readout">
      <span>Pontos registrados</span>
      <strong id="trackCount">0</strong>
    </div>
  </section>

</div>

<div class="section-title">04 / CONTROLES DE VOO GPS</div>

<div class="buttons">
  <button id="locate" class="primary">
    ◎ Minha localização
  </button>

  <button id="start" class="green">
    ▶ Iniciar registro
  </button>

  <button id="stop" disabled>
    ■ Parar registro
  </button>

  <button id="setHome" class="warning">
    ⌂ Definir início
  </button>
</div>

<div class="section-title">05 / MEDIDOR DE TERRENOS</div>

<div class="metrics">

  <div class="card">
    <small>ÁREA</small>
    <strong id="area">0</strong>
    <span class="unit">m²</span>
  </div>

  <div class="card">
    <small>PERÍMETRO</small>
    <strong id="perimeter">0</strong>
    <span class="unit">m</span>
  </div>

  <div class="card">
    <small>HECTARES</small>
    <strong id="hectares">0</strong>
  </div>

  <div class="card">
    <small>PONTOS DO TERRENO</small>
    <strong id="pointCount">0</strong>
  </div>

</div>

<div class="buttons">

  <button id="markCurrent">
    ＋ Marcar posição
  </button>

  <button id="closePolygon">
    Fechar polígono
  </button>

  <button id="undo">
    ↶ Desfazer ponto
  </button>

  <button id="clear">
    Limpar medição
  </button>

</div>

<div class="section-title">06 / DADOS E HISTÓRICO</div>

<div class="buttons">

  <button id="export" class="primary">
    ⇩ Exportar CSV
  </button>

  <button id="save" class="green">
    ▣ Salvar trajeto
  </button>

  <button id="historyButton">
    ◷ Ver histórico
  </button>

  <button id="clearHistory" class="danger">
    Apagar histórico
  </button>

</div>

<section class="panel">
  <div class="panel-title">CENTRAL DE STATUS</div>

  <div id="message" role="status" aria-live="polite">
    Toque em Minha localização e autorize o GPS.
  </div>

  <div class="muted" style="margin-top:12px">
    Este painel utiliza os sensores e o GPS do celular.
    Ele não recebe automaticamente a telemetria do drone.
    Altitude, velocidade e direção podem não estar disponíveis
    ou apresentar imprecisões.
    Não é um sistema de controle de voo.
  </div>
</section>

<section class="panel">
  <div class="panel-title">HISTÓRICO LOCAL</div>
  <div id="history">Nenhum trajeto salvo.</div>
</section>

<p class="muted">
  GeoMeasure Flight 2.0 • OpenStreetMap contributors.
  Requer internet e permissão de localização.
</p>

</main>

<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>

<script>
(() => {

const $ = id => document.getElementById(id);

const R = 6371008.8;

const map = L.map("map").setView([-14.235,-51.925],4);

L.tileLayer(
  "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
  {
    maxZoom:20,
    attribution:"© OpenStreetMap contributors"
  }
).addTo(map);

// ESTADO DO SISTEMA

let current = null;
let watchId = null;

let track = [];
let trackLine = null;
let userMarker = null;
let homeMarker = null;
let homePoint = null;

let totalDistance = 0;
let lastTrackPoint = null;

let points = [];
let markers = [];
let line = null;
let polygon = null;
let closed = false;

// FUNÇÕES GEOGRÁFICAS

const rad = n => n * Math.PI / 180;

function distance(a,b) {

  const p1 = rad(a.lat);
  const p2 = rad(b.lat);

  const dp = rad(b.lat-a.lat);
  const dl = rad(b.lng-a.lng);

  const h =
    Math.sin(dp/2)**2 +
    Math.cos(p1)*Math.cos(p2)*
    Math.sin(dl/2)**2;

  return 2 * R * Math.asin(Math.sqrt(h));
}

function format(n,d=2) {
  return Number(n).toLocaleString("pt-BR",{
    maximumFractionDigits:d
  });
}

// ÁREA DO TERRENO

function calculateArea() {

  if (!closed || points.length < 3) return 0;

  const lat0 =
    points.reduce((s,p)=>s+p.lat,0) /
    points.length * Math.PI/180;

  const xy = points.map(p=>({
    x:R*p.lng*Math.PI/180*Math.cos(lat0),
    y:R*p.lat*Math.PI/180
  }));

  let sum = 0;

  for(let i=0;i<xy.length;i++) {

    const a = xy[i];
    const b = xy[(i+1)%xy.length];

    sum += a.x*b.y - b.x*a.y;
  }

  return Math.abs(sum/2);
}

function calculatePerimeter() {

  if(points.length<2) return 0;

  let sum=0;

  for(let i=0;i<points.length-1;i++) {
    sum+=distance(points[i],points[i+1]);
  }

  if(closed) {
    sum+=distance(
      points[points.length-1],
      points[0]
    );
  }

  return sum;
}

// ATUALIZA MEDIÇÃO

function renderMeasurement() {

  markers.forEach(m=>map.removeLayer(m));
  markers=[];

  if(line) map.removeLayer(line);
  if(polygon) map.removeLayer(polygon);

  points.forEach((p,i)=>{

    const marker = L.circleMarker(
      [p.lat,p.lng],
      {
        radius:6,
        color:"#ffffff",
        weight:2,
        fillColor:"#31d99a",
        fillOpacity:1
      }
    ).addTo(map);

    marker.bindTooltip("Ponto "+(i+1));

    markers.push(marker);
  });

  if(points.length>1) {

    line=L.polyline(
      points.map(p=>[p.lat,p.lng]),
      {
        color:"#31d99a",
        weight:3,
        dashArray:closed?null:"7 6"
      }
    ).addTo(map);
  }

  if(closed && points.length>=3) {

    polygon=L.polygon(
      points.map(p=>[p.lat,p.lng]),
      {
        color:"#31d99a",
        fillColor:"#31d99a",
        fillOpacity:.18
      }
    ).addTo(map);
  }

  const area=calculateArea();

  $("area").textContent=format(area);
  $("perimeter").textContent=format(calculatePerimeter());
  $("hectares").textContent=format(area/10000,5);
  $("pointCount").textContent=points.length;
}

function addPoint(p) {

  if(closed) {
    $("message").textContent=
      "O polígono está fechado. Desfaça ou limpe a medição.";
    return;
  }

  if(
    points.length &&
    distance(points[points.length-1],p)<1.5
  ) return;

  points.push({
    lat:p.lat,
    lng:p.lng,
    accuracy:p.accuracy||null
  });

  renderMeasurement();
}

// BÚSSOLA

function directionName(deg) {

  const directions=[
    "Norte","Nordeste","Leste","Sudeste",
    "Sul","Sudoeste","Oeste","Noroeste"
  ];

  return directions[
    Math.round(deg/45)%8
  ];
}

function updateHeading(deg) {

  deg=(deg+360)%360;

  $("heading").textContent=Math.round(deg)+"°";
  $("direction").textContent=directionName(deg);

  $("needle").style.transform=
    "rotate("+deg+"deg)";
}

function onOrientation(event) {

  let heading=null;

  if(typeof event.webkitCompassHeading==="number") {
    heading=event.webkitCompassHeading;
  } else if(typeof event.alpha==="number") {
    heading=360-event.alpha;
  }

  if(heading!==null) updateHeading(heading);
}

async function activateCompass() {

  try {

    if(
      typeof DeviceOrientationEvent!=="undefined" &&
      typeof DeviceOrientationEvent.requestPermission==="function"
    ) {

      const result=
        await DeviceOrientationEvent.requestPermission();

      if(result!=="granted") {
        $("message").textContent=
          "Permissão da bússola não concedida.";
        return;
      }
    }

    window.addEventListener(
      "deviceorientation",
      onOrientation,
      true
    );

    $("message").textContent=
      "Sensor de orientação ativado, se disponível no aparelho.";

  } catch(e) {

    $("message").textContent=
      "Não foi possível ativar a bússola neste aparelho.";
  }
}

// GPS

function setStatus(text,active=false) {

  $("gpsStatus").textContent=text;

  $("gpsStatus").style.color=
    active?"#31d99a":"#f5c65b";

  $("gpsStatus").style.borderColor=
    active?"#267d60":"#78612a";
}

function onPosition(position) {

  const coords=position.coords;

  const c={
    lat:coords.latitude,
    lng:coords.longitude,
    accuracy:coords.accuracy,
    altitude:coords.altitude,
    speed:coords.speed
  };

  current=c;

  $("lat").textContent=c.lat.toFixed(6);
  $("lng").textContent=c.lng.toFixed(6);

  $("accuracy").textContent=
    Math.round(c.accuracy);

  $("altitude").textContent=
    c.altitude===null?"—":format(c.altitude,1);

  $("speed").textContent=
    c.speed===null?"—":format(c.speed*3.6,1);

  setStatus(
    watchId!==null?"REGISTRANDO":"GPS ATIVO",
    true
  );

  if(!userMarker) {

    userMarker=L.circleMarker(
      [c.lat,c.lng],
      {
        radius:9,
        color:"#ffffff",
        weight:3,
        fillColor:"#36bdf5",
        fillOpacity:1
      }
    ).addTo(map).bindTooltip("Posição do celular / piloto");

  } else {

    userMarker.setLatLng([c.lat,c.lng]);
  }

  if(!map._centered) {

    map.setView([c.lat,c.lng],17);
    map._centered=true;
  }

  if(watchId!==null && c.accuracy<=30) {

    if(
      !lastTrackPoint ||
      distance(lastTrackPoint,c)>=2
    ) {

      if(lastTrackPoint) {

        const segment=distance(lastTrackPoint,c);

        totalDistance+=segment;

        const y=Math.sin(rad(c.lng-lastTrackPoint.lng))*
          Math.cos(rad(c.lat));

        const x=Math.cos(rad(lastTrackPoint.lat))*
          Math.sin(rad(c.lat))-
          Math.sin(rad(lastTrackPoint.lat))*
          Math.cos(rad(c.lat))*
          Math.cos(rad(c.lng-lastTrackPoint.lng));

        const bearing=
          (Math.atan2(y,x)*180/Math.PI+360)%360;

        updateHeading(bearing);
      }

      const record={
        lat:c.lat,
        lng:c.lng,
        accuracy:c.accuracy,
        speed:c.speed,
        altitude:c.altitude,
        time:new Date().toISOString()
      };

      track.push(record);
      lastTrackPoint=c;

      $("distance").textContent=format(totalDistance,1);
      $("trackCount").textContent=track.length;

      if(trackLine) map.removeLayer(trackLine);

      if(track.length>1) {

        trackLine=L.polyline(
          track.map(p=>[p.lat,p.lng]),
          {
            color:"#36d9f5",
            weight:4
          }
        ).addTo(map);
      }
    }
  }
}

function onError(error) {

  let message="Erro ao obter localização.";

  if(error.code===1) {
    message="Permissão de localização negada.";
  }

  if(error.code===2) {
    message="Localização indisponível. Verifique o GPS.";
  }

  if(error.code===3) {
    message="Tempo esgotado ao obter localização.";
  }

  $("message").textContent=message;
  setStatus("GPS INDISPONÍVEL");
}

function locate() {

  if(!navigator.geolocation) {

    $("message").textContent=
      "Seu navegador não oferece geolocalização.";

    return;
  }

  $("message").textContent=
    "Solicitando localização do aparelho...";

  navigator.geolocation.getCurrentPosition(
    position=>{
      onPosition(position);

      $("message").textContent=
        "Localização encontrada. Precisão aproximada: "+
        Math.round(position.coords.accuracy)+" metros.";
    },
    onError,
    {
      enableHighAccuracy:true,
      timeout:20000,
      maximumAge:0
    }
  );
}

// INICIAR E PARAR REGISTRO

function startTracking() {

  if(!navigator.geolocation) {

    $("message").textContent=
      "GPS não disponível neste navegador.";

    return;
  }

  if(watchId!==null) return;

  lastTrackPoint=
    track.length?track[track.length-1]:null;

  watchId=navigator.geolocation.watchPosition(
    position=>{

      onPosition(position);

      if(position.coords.accuracy>30) {

        $("message").textContent=
          "Precisão baixa. Aguardando sinal GPS melhor.";

      } else {

        $("message").textContent=
          "Registro GPS ativo. Acompanhando o celular/piloto.";
      }
    },
    onError,
    {
      enableHighAccuracy:true,
      maximumAge:1000,
      timeout:20000
    }
  );

  $("start").disabled=true;
  $("stop").disabled=false;

  setStatus("REGISTRANDO",true);
}

function stopTracking() {

  if(watchId!==null) {

    navigator.geolocation.clearWatch(watchId);
  }

  watchId=null;

  $("start").disabled=false;
  $("stop").disabled=true;

  setStatus(current?"GPS EM ESPERA":"GPS DESLIGADO");

  $("message").textContent=
    "Registro interrompido.";
}

// PONTO INICIAL

function setHome() {

  if(!current) {

    locate();

    $("message").textContent=
      "Obtenha sua localização e defina o início novamente.";

    return;
  }

  homePoint={
    lat:current.lat,
    lng:current.lng
  };

  if(homeMarker) map.removeLayer(homeMarker);

  homeMarker=L.marker(
    [homePoint.lat,homePoint.lng]
  ).addTo(map).bindTooltip("Ponto inicial");

  $("message").textContent=
    "Ponto inicial definido.";
}

// DISTÂNCIA DO INÍCIO

function updateHomeDistance() {

  if(!homePoint || !current) {
    $("homeDistance").textContent="—";
    return;
  }

  $("homeDistance").textContent=
    format(distance(homePoint,current),1)+" m";
}

// EVENTOS DOS BOTÕES

$("locate").onclick=locate;

$("start").onclick=startTracking;

$("stop").onclick=stopTracking;

$("setHome").onclick=setHome;

$("compassButton").onclick=activateCompass;

$("markCurrent").onclick=()=>{

  if(current) {
    addPoint(current);
  } else {
    locate();
  }
};

$("closePolygon").onclick=()=>{

  if(points.length<3) {

    $("message").textContent=
      "Marque pelo menos três pontos.";

    return;
  }

  closed=true;

  renderMeasurement();

  $("message").textContent=
    "Polígono fechado. Área calculada.";
};

$("undo").onclick=()=>{

  if(closed) closed=false;

  points.pop();

  renderMeasurement();
};

$("clear").onclick=()=>{

  points=[];
  closed=false;

  renderMeasurement();

  $("message").textContent=
    "Medição de terreno apagada.";
};

map.on("click",event=>{

  addPoint({
    lat:event.latlng.lat,
    lng:event.latlng.lng
  });

  $("message").textContent=
    "Ponto manual adicionado ao terreno.";
});

// HISTÓRICO LOCAL

function getHistory() {

  try {

    return JSON.parse(
      localStorage.getItem("geomeasureFlightHistory")||"[]"
    );

  } catch(e) {

    return [];
  }
}

function showHistory() {

  const history=getHistory();

  if(!history.length) {

    $("history").textContent=
      "Nenhum trajeto salvo.";

    return;
  }

  $("history").innerHTML=hi
