<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>GPSpace — All-in-One Map • Chat • Friends • Forum • Commands</title>

<!-- Leaflet -->
<link rel="stylesheet" href="https://unpkg.com/leaflet/dist/leaflet.css" />
<script src="https://unpkg.com/leaflet/dist/leaflet.js"></script>

<!-- Firebase compat (optional) -->
<script src="https://www.gstatic.com/firebasejs/10.12.4/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.12.4/firebase-auth-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.12.4/firebase-firestore-compat.js"></script>

<style>
:root{
  --bg:#0b1220; --card:#0f1a2b; --muted:#8ca3c7; --text:#e6f1ff;
  --brand:#6ee7ff; --brand2:#a78bfa; --accent:#f472b6;
  --ring:0 8px 30px rgba(111,171,255,.25);
}
*{box-sizing:border-box}
html,body{height:100%}
body{
  margin:0;font-family:Inter,system-ui,Segoe UI,Roboto,Arial,sans-serif;
  background:radial-gradient(1200px 600px at 20% -10%, #13203a 0%, #0b1220 60%), #0b1220;
  color:var(--text);display:flex;flex-direction:column;
}
header{
  position:relative;z-index:1200;display:flex;align-items:center;justify-content:space-between;
  padding:12px 14px;background:linear-gradient(90deg,#14223d,#101a2f);
  border-bottom:1px solid #162741;box-shadow:var(--ring)
}
.brand{display:flex;align-items:center;gap:10px;font-weight:800}
.orb{width:28px;height:28px;border-radius:50%;background:conic-gradient(from 0deg,var(--brand),var(--accent),var(--brand2),var(--brand));box-shadow:0 0 20px #7dd3fc66}
.row{display:flex;gap:8px;align-items:center}
.pill{padding:6px 10px;border-radius:999px;background:#0e1b31;border:1px solid #274062;color:#d7e6ff}
button{border:none;border-radius:12px;padding:8px 12px;background:linear-gradient(180deg,#1f3b66,#122747);color:#d7e6ff;cursor:pointer;transition:.15s;box-shadow:0 6px 18px rgba(67,129,255,.12)}
button:hover{filter:brightness(1.07);transform:translateY(-1px)}
.ghost{background:#0e1b31;border:1px solid #2a4365}
.danger{background:linear-gradient(180deg,#5a2330,#3a1018)}
#map{flex:1;position:relative;z-index:0}

/* Panels */
.panel{position:absolute;background:linear-gradient(180deg,#0f1a2b,#0b1423);border:1px solid #183051;border-radius:12px;padding:8px;box-shadow:0 8px 30px rgba(2,8,23,.45);z-index:1100}
#searchBar{top:64px;left:50%;transform:translateX(-50%);display:flex;gap:6px}
#searchBar input{width:260px;padding:8px 10px;border-radius:10px;border:1px solid #244267;background:#0c1424;color:#e6f1ff}
#styleBox{top:64px;right:10px;width:220px}
#styleBox select{width:100%;padding:8px 10px;border-radius:10px;border:1px solid #244267;background:#0c1424;color:#e6f1ff}
#teleportBox{top:120px;right:10px;width:240px}
#teleportBox input{width:64px;padding:6px 8px;border-radius:8px;border:1px solid #244267;background:#0c1424;color:#e6f1ff}
#teleHistory{max-height:100px;overflow:auto;margin-top:6px;font-size:12px;color:var(--muted)}
#myLocationBtn{top:64px;left:10px;padding:8px;display:block}
#chatBox{bottom:10px;right:10px;width:340px;height:300px;display:flex;flex-direction:column}
#chatMessages{flex:1;overflow:auto;background:#0a0f1b;border:1px solid #203a60;border-radius:10px;padding:8px;margin:6px 0;font-size:14px}
#chatInput{flex:1;padding:8px;border-radius:10px;border:1px solid #244267;background:#0c1424;color:#e6f1ff}
#friendsBox{top:220px;right:10px;width:240px}
#friendList{max-height:140px;overflow:auto;margin-top:6px;font-size:14px}
#pinBox{top:120px;left:10px;width:320px}
#pinTitle{width:100%;padding:8px 10px;border-radius:10px;border:1px solid #244267;background:#0c1424;color:#e6f1ff}
#pinList{max-height:160px;overflow:auto;margin-top:6px;font-size:13px}
#peopleBox{top:10px;right:260px;width:220px}

/* Place modal */
#placeModal{position:fixed;left:50%;top:50%;transform:translate(-50%,-50%);width:360px;max-width:94vw;z-index:1400;display:none;padding:12px;border-radius:12px;border:1px solid #23364f;background:linear-gradient(180deg,#0f1724,#071222);box-shadow:0 20px 60px rgba(0,0,0,.6)}
#placeModal input,#placeModal textarea{width:100%;padding:8px;border-radius:8px;border:1px solid #244267;background:#08131f;color:#eaf6ff}
#placeModal .row{margin-top:8px}

/* Command console */
#commandBox{bottom:260px;right:10px;width:360px;height:160px;display:flex;flex-direction:column}
#commandLog{flex:1;overflow:auto;background:#0a0f1b;border:1px solid #203a60;border-radius:10px;padding:6px;margin:6px 0;font-size:13px}

/* Forum panel */
#forumBox{bottom:10px;left:10px;width:420px;height:420px;display:flex;flex-direction:column}
#forumThreads{flex:1;overflow:auto;background:#07101b;border-radius:8px;padding:8px;border:1px solid #203a60;margin:6px 0}
#forumComposer{display:flex;gap:6px;margin-top:6px}

/* debug */
#debugToggle{position:fixed;bottom:10px;left:10px;z-index:2000}
#debugPanel{position:fixed;left:0;right:0;bottom:0;background:#0a0f1b;color:#9fe8ff;border-top:1px solid #1f385e;max-height:200px;height:160px;overflow:auto;padding:6px;z-index:2000;transform:translateY(100%);transition:.3s}
#debugPanel.active{transform:translateY(0)}
.emoji-btn{cursor:pointer;padding:6px;border-radius:8px;background:#08131f;border:1px solid #203a60;color:#fff}
.active-emoji{box-shadow:0 0 14px rgba(120,115,245,.6);border-color:#a78bfa!important}
.small{font-size:13px;color:var(--muted)}

@media (max-width:900px){
  #forumBox{width:92vw;left:4%;bottom:10px}
  #peopleBox{display:none}
}
</style>
</head>
<body>

<header>
  <div class="brand">
    <div class="orb"></div>
    <div style="display:inline-block">GPSpace</div>
    <span class="pill" id="netStatus">offline</span>
  </div>
  <div class="row">
    <span id="userBadge" class="pill">Guest</span>
    <button id="loginBtn">Sign in</button>
    <button id="logoutBtn" class="danger" style="display:none">Logout</button>
    <button id="ghostToggle" class="ghost" title="Ghost mode (invisible to others)">Ghost: OFF</button>
  </div>
</header>

<div id="map"></div>

<!-- Search Bar -->
<div id="searchBar" class="panel">
  <input id="searchInput" placeholder="Search city, place…" />
  <button id="searchBtn">Go</button>
</div>

<!-- Map Style -->
<div id="styleBox" class="panel">
  <div style="margin-bottom:6px"><b>🗺️ Map Style</b></div>
  <select id="styleSelect">
    <option value="osm">OpenStreetMap</option>
    <option value="carto">Carto Light</option>
    <option value="dark">Dark Matter</option>
    <option value="sat">Esri Satellite</option>
  </select>
  <div style="margin-top:8px">
    <small class="small">You can also create a pin for the current basemap by pressing the pin button in Commands.</small>
  </div>
</div>

<!-- Teleport -->
<div id="teleportBox" class="panel">
  <div><b>⚡ Teleport</b></div>
  <div class="row" style="margin-top:6px">
    <input id="teleLat" placeholder="Lat" />
    <input id="teleLng" placeholder="Lng" />
    <input id="teleZoom" placeholder="Z" style="width:48px" />
    <button id="teleBtn">Go</button>
  </div>
  <div id="teleHistory" class="small"></div>
</div>

<!-- Pin Box -->
<div id="pinBox" class="panel">
  <div style="display:flex; gap:6px; align-items:center; justify-content:space-between">
    <b>📍 Pin & Share</b>
    <button id="shareHereBtn" class="ghost" title="Share current map center">Share Here</button>
  </div>
  <input id="pinTitle" placeholder="Title or command for selected spot…" />
  <div id="pinList" class="small"></div>
</div>

<!-- People Online -->
<div id="peopleBox" class="panel">
  <div style="display:flex;justify-content:space-between;align-items:center">
    <b>🟢 People Online</b>
    <small id="onlineCount" class="small">0</small>
  </div>
  <div id="onlineList" style="margin-top:8px;max-height:160px;overflow:auto;font-size:13px"></div>
</div>

<!-- My Location -->
<button id="myLocationBtn" class="panel">📍 My Location</button>

<!-- Friends -->
<div id="friendsBox" class="panel">
  <div><b>👥 Friends</b></div>
  <div class="row" style="margin-top:6px">
    <input id="friendName" placeholder="Friend name" style="flex:1;padding:6px;border-radius:8px;border:1px solid #244267;background:#0c1424;color:#e6f1ff">
    <button id="addFriendBtn">＋</button>
  </div>
  <div id="friendList" style="margin-top:8px"></div>
</div>

<!-- Chat -->
<div id="chatBox" class="panel">
  <b>💬 Chat</b>
  <div id="chatMessages"></div>
  <div class="row">
    <input id="chatInput" placeholder="Type message…" />
    <button id="sendChatBtn">Send</button>
    <button id="shareLocationBtn" title="Share current map center">Share Loc</button>
  </div>
</div>

<!-- Command Console -->
<div id="commandBox" class="panel">
  <b>⌨️ Commands</b>
  <div id="commandLog"></div>
  <div class="row" style="margin-top:6px">
    <input id="commandInput" placeholder="Type /help for list…" style="flex:1;padding:6px;border-radius:8px;border:1px solid #244267;background:#0c1424;color:#e6f1ff" />
    <button id="runCommandBtn">Run</button>
  </div>
</div>

<!-- Place Modal -->
<div id="placeModal">
  <div style="display:flex;justify-content:space-between;align-items:center">
    <b>📍 Add Place</b>
    <button id="closePlaceModal">✕</button>
  </div>
  <div style="margin-top:8px">
    <label>Emoji:</label>
    <input id="placeEmoji" placeholder="🏠" style="width:40px" />
  </div>
  <div class="row"><input id="placeTitle" placeholder="Title…" /></div>
  <div class="row"><textarea id="placeDesc" placeholder="Description…" rows="3"></textarea></div>
  <div class="row" style="justify-content:flex-end;margin-top:6px">
    <button id="savePlaceBtn">Save</button>
  </div>
</div>

<!-- Forum -->
<div id="forumBox" class="panel">
  <b>📝 Forum</b>
  <div id="forumThreads"></div>
  <div id="forumComposer" class="row">
    <input id="forumInput" placeholder="New post…" style="flex:1;padding:6px;border-radius:8px;border:1px solid #244267;background:#0c1424;color:#e6f1ff">
    <button id="forumPostBtn">Post</button>
  </div>
</div>

<!-- Debug -->
<button id="debugToggle">🐞 Debug</button>
<div id="debugPanel"></div>

<script>
/* ================= Utilities ================= */
const $ = id => document.getElementById(id);
function esc(s=''){ return String(s).replace(/[&<>"']/g, m=>({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;'}[m])); }
function logDebug(msg){ const el=document.createElement('div'); el.textContent = '[' + new Date().toLocaleTimeString() + '] ' + msg; $('debugPanel').appendChild(el); $('debugPanel').scrollTop = $('debugPanel').scrollHeight; }

/* ================= App state ================= */
const state = {
  uid: null, name: 'Guest', emoji: '🙂', ghost: false,
  pins: {}, online: {}, friends: [], teleHistory: []
};

/* ================= Map & Tiles ================= */
const map = L.map('map', {zoomControl: true}).setView([20,0], 2);
const tiles = {
  osm: L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',{attribution:'© OpenStreetMap'}),
  carto: L.tileLayer('https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}{r}.png',{attribution:'© CartoDB'}),
  dark: L.tileLayer('https://{s}.basemaps.cartocdn.com/dark_all/{z}/{x}/{y}{r}.png',{attribution:'© CartoDB'}),
  sat: L.tileLayer('https://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}',{attribution:'© Esri'})
};
tiles.osm.addTo(map);

$('styleSelect').addEventListener('change', e => {
  for (let k in tiles) if (map.hasLayer(tiles[k])) map.removeLayer(tiles[k]);
  tiles[e.target.value].addTo(map);
  // Add a command entry when style is selected (so commands include map style)
  appendCommandLog('Map style set to ' + e.target.value);
});

/* ================= Firebase init (optional) ================= */
/* Replace the config below with your Firebase project's keys.
   If you leave placeholders, Firestore features will error — the UI still works locally. */
const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT.firebaseapp.com",
  projectId: "YOUR_PROJECT",
  storageBucket: "YOUR_PROJECT.appspot.com",
  messagingSenderId: "YOUR_SENDER_ID",
  appId: "YOUR_APP_ID"
};

let fbAvailable = false;
let db = null;
try {
  if (firebase && firebase.initializeApp) {
    firebase.initializeApp(firebaseConfig);
    const auth = firebase.auth();
    db = firebase.firestore();
    fbAvailable = true;
    $('netStatus').textContent = 'online';
    logDebug('Firebase initialized — realtime features available.');
  }
} catch (e) {
  fbAvailable = false;
  $('netStatus').textContent = 'offline';
  logDebug('Firebase not configured / init failed — running local-only.');
}

/* ================= Auth & Presence ================= */
if (fbAvailable) {
  const auth = firebase.auth();
  auth.onAuthStateChanged(user => {
    if (user) {
      state.uid = user.uid;
      state.name = user.displayName || user.email || ('user-' + user.uid.slice(0,6));
      $('userBadge').textContent = `${state.emoji} ${state.name}`;
      $('loginBtn').style.display = 'none';
      $('logoutBtn').style.display = 'inline-block';
      setPresence();
      startRealtime();
      logDebug('Signed in as ' + state.name);
    } else {
      state.uid = null;
      state.name = 'Guest';
      $('userBadge').textContent = 'Guest';
      $('loginBtn').style.display = 'inline-block';
      $('logoutBtn').style.display = 'none';
      removePresence();
      stopRealtime();
    }
  });
  $('loginBtn').onclick = () => {
    // simple Google popup login
    const provider = new firebase.auth.GoogleAuthProvider();
    auth.signInWithPopup(provider).catch(err => logDebug('Login error: ' + err.message));
  };
  $('logoutBtn').onclick = () => auth.signOut();
} else {
  // local demo login
  $('loginBtn').onclick = () => {
    const name = prompt('Demo login name', 'Guest') || 'Guest';
    state.name = name;
    state.uid = 'local-' + Math.random().toString(36).slice(2,8);
    $('userBadge').textContent = state.emoji + ' ' + state.name;
    $('loginBtn').style.display = 'none';
    $('logoutBtn').style.display = 'inline-block';
    renderOnlineLocal();
    logDebug('Local demo login: ' + state.name);
  };
  $('logoutBtn').onclick = () => {
    state.uid = null;
    state.name = 'Guest';
    $('userBadge').textContent = 'Guest';
    $('loginBtn').style.display = 'inline-block';
    $('logoutBtn').style.display = 'none';
    renderOnlineLocal();
  };
}

/* Presence (people online) */
async function setPresence(){
  if(!fbAvailable || !state.uid) return;
  if(state.ghost){
    try { await db.collection('presence').doc(state.uid).delete(); } catch(e){/* ignore */ }
    return;
  }
  try {
    await db.collection('presence').doc(state.uid).set({ uid: state.uid, name: state.name, emoji: state.emoji, ts: Date.now() });
  } catch(e){ logDebug('Presence set failed: ' + e.message); }
}
async function removePresence(){
  if(!fbAvailable || !state.uid) return;
  try { await db.collection('presence').doc(state.uid).delete(); } catch(e){/* ignore */ }
}

/* subscribe presence */
if (fbAvailable) {
  db.collection('presence').onSnapshot(snap => {
    state.online = {};
    snap.forEach(d => {
      state.online[d.id] = d.data();
    });
    renderOnline();
  });
} else {
  renderOnlineLocal();
}

/* render online list (from firestore) */
function renderOnline(){
  const list = $('onlineList'); list.innerHTML = '';
  const names = Object.values(state.online || {});
  if(names.length === 0 && !state.uid) names.push({ name: '(no users)', emoji: '👀' });
  names.forEach(u => {
    const div = document.createElement('div');
    div.innerHTML = esc(u.emoji || '🙂') + ' <b>' + esc(u.name || 'anon') + '</b>';
    list.appendChild(div);
  });
  $('onlineCount').textContent = names.length;
}
/* local fallback */
function renderOnlineLocal(){
  const list = $('onlineList'); list.innerHTML = '';
  const names = [];
  if(state.uid) names.push({ name: state.name, emoji: state.emoji });
  else names.push({ name: '(local demo)', emoji: '🙂' });
  names.forEach(u => {
    const div = document.createElement('div');
    div.innerHTML = esc(u.emoji) + ' <b>' + esc(u.name) + '</b>';
    list.appendChild(div);
  });
  $('onlineCount').textContent = names.length;
}

/* ================= Chat ================= */
const chatMessages = $('chatMessages');
function appendChatMessage(uid, name, emoji, text) {
  const div = document.createElement('div');
  div.innerHTML = '<b>' + esc(name || uid) + ' ' + esc(emoji || '') + '</b>: ' + esc(text);
  chatMessages.appendChild(div);
  chatMessages.scrollTop = chatMessages.scrollHeight;
}

if (fbAvailable) {
  // realtime chat subscription
  db.collection('chat').orderBy('time', 'asc').limitToLast(500)
    .onSnapshot(snap => {
      chatMessages.innerHTML = '';
      snap.forEach(doc => {
        const m = doc.data();
        appendChatMessage(m.uid, m.name || m.uid, m.emoji || '', m.text);
      });
    });
}
// send chat button
$('sendChatBtn').onclick = () => {
  const txt = $('chatInput').value.trim();
  if (!txt) return;
  const payload = {
    text: txt,
    uid: state.uid || ('guest-' + Math.random().toString(36).slice(2,6)),
    name: state.name,
    emoji: state.emoji,
    time: Date.now()
  };
  if (fbAvailable) {
    db.collection('chat').add(payload).catch(e => logDebug('Chat send failed: ' + e.message));
  } else {
    appendChatMessage(payload.uid, payload.name, payload.emoji, payload.text);
  }
  $('chatInput').value = '';
};

/* share location to chat */
$('shareLocationBtn').onclick = () => {
  const c = map.getCenter();
  const msg = `Shared location: ${c.lat.toFixed(5)}, ${c.lng.toFixed(5)}`;
  if (fbAvailable) {
    db.collection('chat').add({ text: msg, uid: state.uid || 'guest', name: state.name, emoji: state.emoji, time: Date.now() });
  } else {
    appendChatMessage('local', state.name, state.emoji, msg);
  }
};

/* ================= Friends ================= */
$('addFriendBtn').onclick = async () => {
  const name = $('friendName').value.trim();
  if (!name) return;
  state.friends.push(name);
  renderFriends();
  $('friendName').value = '';
  if (fbAvailable && state.uid && !state.ghost) {
    try {
      await db.collection('users').doc(state.uid).collection('friends').add({ name, ts: Date.now() });
    } catch(e) { logDebug('Save friend failed: '+ e.message); }
  }
};
function renderFriends(){
  $('friendList').innerHTML = state.friends.map(n => '<div>🟢 ' + esc(n) + '</div>').join('');
}

/* ================= Pins (map markers) ================= */
const pinLayer = L.layerGroup().addTo(map);

function addPinToMap(pin) {
  // pin: { id, lat, lng, title, emoji, uid, name, ts, commands }
  if (!pin || !('id' in pin)) return;
  if (state.pins[pin.id]) return;
  const color = '#6ee7ff';
  const html = `<div style="width:18px;height:18px;border-radius:50%;background:${color};box-shadow:0 0 8px ${color};border:2px solid #fff"></div>`;
  const ic = L.divIcon({ html: html, className: '', iconSize: [18,18] });
  const m = L.marker([pin.lat, pin.lng], { icon: ic }).addTo(pinLayer);
  const popupParts = [];
  popupParts.push('<b>' + esc(pin.title || 'Untitled') + '</b>');
  popupParts.push('by ' + esc(pin.name || pin.uid || 'anon') + ' ' + esc(pin.emoji || ''));
  if (pin.commands && pin.commands.length) popupParts.push('<small>Commands: ' + esc(pin.commands.join(', ')) + '</small>');
  popupParts.push('<small>' + new Date(pin.ts || Date.now()).toLocaleString() + '</small>');
  m.bindPopup(popupParts.join('<br>'));
  state.pins[pin.id] = m;
}

/* load pins realtime if possible */
if (fbAvailable) {
  db.collection('pins').orderBy('ts', 'desc').limit(1000).onSnapshot(snap => {
    snap.docChanges().forEach(ch => {
      const data = ch.doc.data();
      const pin = {
        id: ch.doc.id,
        lat: data.lat,
        lng: data.lng,
        title: data.title,
        emoji: data.emoji,
        uid: data.uid,
        name: data.name,
        ts: data.ts,
        commands: data.commands || []
      };
      if (ch.type === 'added') addPinToMap(pin);
      if (ch.type === 'removed') {
        if (state.pins[pin.id]) { map.removeLayer(state.pins[pin.id]); delete state.pins[pin.id]; }
      }
    });
    renderPinList();
  });
}

/* local-add pin (used when offline) */
function addLocalPinObj(pin) {
  const id = 'local-' + Math.random().toString(36).slice(2,10);
  addPinToMap({ ...pin, id: id });
  renderPinList();
}

/* render list of pins so user can jump to them */
function renderPinList(){
  const out = [];
  for (const id in state.pins) {
    try {
      const m = state.pins[id];
      const ll = m.getLatLng();
      out.push(`<div style="display:flex;justify-content:space-between;gap:6px;align-items:center;margin:4px 0">
        <span>${ll.lat.toFixed(4)}, ${ll.lng.toFixed(4)}</span>
        <button class="ghost" data-id="${id}">Go</button>
      </div>`);
    } catch(e) { /* ignore */ }
  }
  $('pinList').innerHTML = out.join('');
  // attach handlers
  document.querySelectorAll('#pinList button[data-id]').forEach(b => {
    b.onclick = () => {
      const id = b.getAttribute('data-id');
      const m = state.pins[id];
      if (!m) return;
      map.setView(m.getLatLng(), 14);
      m.openPopup();
    };
  });
}

/* click map to open place modal (pre-fill center location in note) */
let tempLatLng = null;
map.on('click', e => {
  tempLatLng = e.latlng;
  openPlaceModal(e.latlng);
});

function openPlaceModal(latlng){
  $('placeEmoji').value = '📍';
  $('placeTitle').value = '';
  $('placeDesc').value = `Coordinates: ${latlng.lat.toFixed(5)}, ${latlng.lng.toFixed(5)}`;
  $('placeModal').style.display = 'block';
}
$('closePlaceModal').onclick = () => { $('placeModal').style.display = 'none'; tempLatLng = null; };

$('savePlaceBtn').onclick = async () => {
  if (!tempLatLng) {
    // fallback to center
    tempLatLng = map.getCenter();
  }
  const emoji = $('placeEmoji').value || '📍';
  const title = $('placeTitle').value.trim() || 'Place';
  const desc = $('placeDesc').value.trim();
  const pinObj = {
    title: title,
    emoji: emoji,
    lat: tempLatLng.lat,
    lng: tempLatLng.lng,
    uid: state.uid || 'local',
    name: state.name,
    ts: Date.now(),
    commands: []
  };
  if (fbAvailable && state.uid && !state.ghost) {
    try {
      await db.collection('pins').add(pinObj);
      logDebug('Saved place to Firestore');
    } catch(e) {
      logDebug('Failed to save place: ' + e.message);
      addLocalPinObj(pinObj);
    }
  } else {
    addLocalPinObj(pinObj);
  }
  $('placeModal').style.display = 'none';
  tempLatLng = null;
};

/* ================= Teleport UI ================= */
$('teleBtn').onclick = () => {
  const lat = parseFloat($('teleLat').value);
  const lng = parseFloat($('teleLng').value);
  const z = parseInt($('teleZoom').value) || map.getZoom();
  if (!isNaN(lat) && !isNaN(lng)) {
    map.setView([lat, lng], z);
    state.teleHistory.unshift({ lat, lng, z, ts: Date.now() });
    state.teleHistory = state.teleHistory.slice(0, 12);
    renderTeleHistory();
    appendCommandLog('Teleported to ' + lat.toFixed(4) + ',' + lng.toFixed(4) + ' z' + z);
  }
};
function renderTeleHistory(){
  $('teleHistory').innerHTML = state.teleHistory.map(h => `<div class="small">${h.lat.toFixed(4)}, ${h.lng.toFixed(4)} z${h.z}</div>`).join('');
}

/* ================= Commands Console ================= */
function appendCommandLog(txt){
  const el = document.createElement('div'); el.textContent = txt; $('commandLog').appendChild(el); $('commandLog').scrollTop = $('commandLog').scrollHeight;
}
$('runCommandBtn').onclick = runCommand;
$('commandInput').addEventListener('keydown', e => { if (e.key === 'Enter') runCommand(); });

function runCommand(){
  const raw = $('commandInput').value.trim();
  if (!raw) return;
  $('commandInput').value = '';
  appendCommandLog('> ' + raw);
  const parts = raw.split(' ').filter(p => p.length);
  const cmd = parts[0].toLowerCase();

  if (cmd === '/help') {
    appendCommandLog('Commands: /teleport lat lng [z] | /mylocation | /friend add NAME | /chat MSG | /pin title | /ghost on|off | /style NAME');
  } else if (cmd === '/teleport') {
    const lat = parseFloat(parts[1]), lng = parseFloat(parts[2]), z = parseInt(parts[3] || map.getZoom());
    if (!isNaN(lat) && !isNaN(lng)) { map.setView([lat,lng], z); appendCommandLog('Teleported'); }
  } else if (cmd === '/mylocation') {
    map.locate({ setView: true, maxZoom: 16 });
  } else if (cmd === '/friend' && parts[1] === 'add') {
    const name = parts.slice(2).join(' ');
    if (name) { state.friends.push(name); renderFriends(); appendCommandLog('Friend added: ' + name); }
  } else if (cmd === '/chat') {
    const msg = parts.slice(1).join(' ');
    if (msg) {
      if (fbAvailable) db.collection('chat').add({ text: msg, uid: state.uid || 'guest', name: state.name, emoji: state.emoji, time: Date.now() });
      else appendChatMessage('local', state.name, state.emoji, msg);
    }
  } else if (cmd === '/pin') {
    const title = parts.slice(1).join(' ') || 'Pin';
    const c = map.getCenter();
    const p = { title, lat: c.lat, lng: c.lng, uid: state.uid || 'local', name: state.name, ts: Date.now(), commands: [] };
    if (fbAvailable && state.uid && !state.ghost) { db.collection('pins').add(p).catch(e => { addLocalPinObj(p); appendCommandLog('Saved local because Firestore failed'); }); }
    else { addLocalPinObj(p); appendCommandLog('Pin added locally'); }
  } else if (cmd === '/ghost') {
    const mode = (parts[1] || '').toLowerCase();
    if (mode === 'on') { state.ghost = true; $('ghostToggle').textContent = 'Ghost: ON'; setPresence(); appendCommandLog('Ghost ON'); }
    if (mode === 'off') { state.ghost = false; $('ghostToggle').textContent = 'Ghost: OFF'; setPresence(); appendCommandLog('Ghost OFF'); }
  } else if (cmd === '/style') {
    const styleName = parts[1];
    if (styleName && tiles[styleName]) {
      for (let k in tiles) if (map.hasLayer(tiles[k])) map.removeLayer(tiles[k]);
      tiles[styleName].addTo(map);
      $('styleSelect').value = styleName;
      appendCommandLog('Map style changed to ' + styleName);
    } else appendCommandLog('Unknown style. Available: osm, carto, dark, sat');
  } else {
    appendCommandLog('Unknown command. Type /help');
  }
}

/* also toggle ghost via header button */
$('ghostToggle').onclick = () => {
  state.ghost = !state.ghost;
  $('ghostToggle').textContent = 'Ghost: ' + (state.ghost ? 'ON' : 'OFF');
  setPresence();
  appendCommandLog('Ghost toggled: ' + state.ghost);
};

/* ================= Share here button (pin at center) ================= */
$('shareHereBtn').onclick = async () => {
  const c = map.getCenter();
  const title = $('pinTitle').value.trim() || 'Shared location';
  const pin = { title, emoji: '📍', lat: c.lat, lng: c.lng, uid: state.uid || 'local', name: state.name, ts: Date.now(), commands: [] };
  if (fbAvailable && state.uid && !state.ghost) {
    try { await db.collection('pins').add(pin); logDebug('Shared pin to Firestore'); }
    catch(e) { addLocalPinObj(pin); logDebug('Shared locally due to error: ' + e.message); }
  } else { addLocalPinObj(pin); logDebug('Shared pin locally'); }
  $('pinTitle').value = '';
};

/* ================= Forum ================= */
$('forumPostBtn').onclick = async () => {
  const txt = $('forumInput').value.trim();
  if (!txt) return;
  const thread = { text: txt, uid: state.uid || 'guest', name: state.name, ts: Date.now() };
  if (fbAvailable && state.uid && !state.ghost) {
    try { await db.collection('forum').add(thread); logDebug('Forum post sent'); }
    catch(e) { // fallback UI
      const row = document.createElement('div'); row.innerHTML = '<b>' + esc(thread.name) + '</b>: ' + esc(thread.text); $('forumThreads').prepend(row);
    }
  } else {
    const row = document.createElement('div'); row.innerHTML = '<b>' + esc(thread.name) + '</b>: ' + esc(thread.text); $('forumThreads').prepend(row);
  }
  $('forumInput').value = '';
};
// live forum stream
if (fbAvailable) {
  db.collection('forum').orderBy('ts','desc').limit(200).onSnapshot(snap => {
    $('forumThreads').innerHTML = '';
    snap.forEach(doc => {
      const t = doc.data();
      const row = document.createElement('div'); row.style.marginBottom = '8px';
      row.innerHTML = '<b>' + esc(t.name || t.uid) + '</b>: ' + esc(t.text || '');
      $('forumThreads').appendChild(row);
    });
  });
}

/* ================= Realtime listeners (start/stop) ================= */
let chatUnsub = null, pinsUnsub = null, forumUnsub = null;
function startRealtime(){
  if (!fbAvailable || !db) return;
  // chat handled above with onSnapshot already
  // pins handled above with onSnapshot already
  // we could add more listeners here if needed
}
function stopRealtime(){
  if (chatUnsub) { chatUnsub(); chatUnsub = null; }
  if (pinsUnsub) { pinsUnsub(); pinsUnsub = null; }
  if (forumUnsub) { forumUnsub(); forumUnsub = null; }
}

/* ================= Debug & Keyboard ================= */
$('debugToggle').onclick = () => { $('debugPanel').classList.toggle('active'); $('debugToggle').textContent = $('debugPanel').classList.contains('active') ? 'Hide Debug' : '🐞 Debug'; };
setInterval(()=>{ $('debugPanel').innerHTML = 'Zoom:' + map.getZoom() + ' Center:' + map.getCenter().lat.toFixed(3) + ',' + map.getCenter().lng.toFixed(3); }, 1000);

window.addEventListener('keydown', e => {
  if (e.key === '/' && document.activeElement.tagName !== 'INPUT' && document.activeElement.tagName !== 'TEXTAREA') {
    e.preventDefault();
    $('commandInput').focus();
  }
});

/* ================= My Location (map locate) ================= */
$('myLocationBtn').onclick = () => {
  if (!navigator.geolocation) return alert('Geolocation not supported');
  navigator.geolocation.getCurrentPosition(pos => {
    map.setView([pos.coords.latitude, pos.coords.longitude], 14);
    const m = L.marker([pos.coords.latitude, pos.coords.longitude]).addTo(map).bindPopup('You are here').openPopup();
    if (!state.ghost) setPresence();
  }, err => alert('Unable to get location: ' + (err.message || 'error')));
};

/* ================= Search (Nominatim) ================= */
$('searchBtn').onclick = async () => {
  const q = $('searchInput').value.trim();
  if (!q) return;
  try {
    const res = await fetch('https://nominatim.openstreetmap.org/search?format=json&q=' + encodeURIComponent(q), { headers: { 'Accept': 'application/json' } });
    const js = await res.json();
    if (js && js.length) {
      const { lat, lon, display_name } = js[0];
      map.flyTo([+lat, +lon], 13, { duration: 1 });
      L.marker([+lat, +lon]).addTo(map).bindPopup(display_name || q).openPopup();
      appendCommandLog('Search -> ' + (display_name || q));
    } else alert('No results');
  } catch (e) { alert('Search failed'); logDebug('Search error: ' + e.message); }
};

/* ================= Init Render & Helpers ================= */
function appendChatDemo(user, text){
  appendChatMessage(user, user, '🙂', text);
}

function appendCommandLogMessage(s){ appendCommandLog(s); }

renderPinList();
renderOnline();
renderFriends();
logDebug('GPSpace final loaded.');

</script>
</body>
</html>
