
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Social App Pro</title>
</head>

<body>

<h2>Réseau Social Pro</h2>

<input id="email" placeholder="email">
<input id="pass" placeholder="password" type="password">
<button onclick="register()">Register</button>
<button onclick="login()">Login</button>

<hr>

<div id="app" style="display:none;">

<h3>Post</h3>
<input id="postText">
<button onclick="createPost()">Send</button>

<h3>IA</h3>
<input id="iaInput">
<button onclick="askAI()">Ask</button>
<div id="iaBox"></div>

<h3>Posts</h3>
<div id="posts"></div>

<h3>Chat</h3>
<input id="msgUser">
<input id="msgText">
<button onclick="sendMsg()">Send</button>
<div id="msgs"></div>

</div>

<script type="module">

import { initializeApp } from "https://www.gstatic.com/firebasejs/12.13.0/firebase-app.js";
import { getAuth, createUserWithEmailAndPassword, signInWithEmailAndPassword, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/12.13.0/firebase-auth.js";
import { getFirestore, collection, addDoc, onSnapshot, query, orderBy } from "https://www.gstatic.com/firebasejs/12.13.0/firebase-firestore.js";

const firebaseConfig = {
  apiKey: "TON_KEY",
  authDomain: "TON_DOMAIN",
  projectId: "TON_ID"
};

const app = initializeApp(firebaseConfig);
const auth = getAuth(app);
const db = getFirestore(app);

let currentUser = null;

/* AUTH */
window.register = () => createUserWithEmailAndPassword(auth, email.value, pass.value);
window.login = () => signInWithEmailAndPassword(auth, email.value, pass.value);

/* STATE */
onAuthStateChanged(auth, (user) => {
  if (user) {
    currentUser = user.email;
    document.getElementById("app").style.display = "block";
    loadPosts();
    loadMsgs();
  }
});

/* POSTS */
window.createPost = async () => {
  await addDoc(collection(db,"posts"), {
    text: postText.value,
    user: currentUser,
    time: Date.now()
  });
};

function loadPosts(){
  onSnapshot(query(collection(db,"posts"), orderBy("time","desc")), snap=>{
    posts.innerHTML="";
    snap.forEach(d=>{
      posts.innerHTML += `<div>${d.data().user}: ${d.data().text}</div>`;
    });
  });
}

/* MESSAGES */
window.sendMsg = async () => {
  await addDoc(collection(db,"msgs"), {
    u: msgUser.value,
    t: msgText.value
  });
};

function loadMsgs(){
  onSnapshot(collection(db,"msgs"), snap=>{
    msgs.innerHTML="";
    snap.forEach(d=>{
      msgs.innerHTML += `<div>${d.data().u}: ${d.data().t}</div>`;
    });
  });
}

/* IA (FAKE CONNECTOR) */
window.askAI = async () => {
  iaBox.innerHTML += "<div>IA: (connect backend OpenAI ici)</div>";
};

</script>

</body>
</html>
<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<title>Réseau Social Ultimate</title>

<style>
body {
  font-family: Arial;
  background: #f0f2f5;
  margin: 0;
}

header {
  background: #1877f2;
  color: white;
  text-align: center;
  padding: 15px;
  font-size: 22px;
  font-weight: bold;
}

.container {
  width: 95%;
  max-width: 500px;
  margin: auto;
}

.card {
  background: white;
  padding: 12px;
  margin-top: 12px;
  border-radius: 12px;
  box-shadow: 0 2px 6px rgba(0,0,0,0.1);
}

input, button {
  width: 100%;
  padding: 10px;
  margin-top: 8px;
  border-radius: 10px;
  border: 1px solid #ccc;
}

button {
  background: #1877f2;
  color: white;
  border: none;
  cursor: pointer;
}

.post {
  background: white;
  padding: 12px;
  margin-top: 10px;
  border-radius: 12px;
}

.name {
  color: #1877f2;
  font-weight: bold;
}

.like {
  background: none;
  border: none;
  color: #e0245e;
  cursor: pointer;
}
img {
  width: 100%;
  border-radius: 10px;
  margin-top: 8px;
}
small { color: gray; }
</style>
</head>

<body>

<header>Réseau Social Ultimate</header>

<div class="container">

<!-- AUTH -->
<div class="card">
  <h3>Connexion</h3>
  <input id="email" placeholder="Email">
  <input id="password" type="password" placeholder="Mot de passe">
  <button id="register">Créer compte</button>
  <button id="login">Connexion</button>
</div>

<!-- APP -->
<div id="app" style="display:none;">

  <div class="card">
    <h3 id="welcome"></h3>
    <button onclick="logout()" style="background:red;">Déconnexion</button>
  </div>

  <!-- POST -->
  <div class="card">
    <h3>Créer post</h3>
    <input id="text" placeholder="Message">
    <input type="file" id="img">
    <button onclick="createPost()">Publier</button>
  </div>

  <!-- IA -->
  <div class="card">
    <h3>IA 🤖</h3>
    <div id="chat" style="height:120px; overflow:auto; background:#f7f7f7; padding:10px;"></div>
    <input id="iaInput" placeholder="Question">
    <button onclick="askAI()">Envoyer</button>
  </div>

  <!-- CHAT -->
  <div class="card">
    <h3>Messagerie 💬</h3>
    <input id="toUser" placeholder="Utilisateur">
    <input id="msgText" placeholder="Message">
    <button onclick="sendMsg()">Envoyer</button>
    <div id="msgs"></div>
  </div>

  <!-- ADMIN PANEL -->
  <div class="card" id="adminPanel" style="display:none;">
    <h3>ADMIN PANEL 🧑‍💼</h3>
    <button onclick="deleteAllPosts()" style="background:red;">Supprimer tous les posts</button>
    <button onclick="deleteAllMsgs()" style="background:red;">Supprimer messages</button>
    <button onclick="banAll()">BAN mode</button>
  </div>

  <!-- POSTS -->
  <div id="posts"></div>

</div>

</div>

<!-- FIREBASE -->
<script type="module">

import { initializeApp } from "https://www.gstatic.com/firebasejs/12.13.0/firebase-app.js";
import { getAuth, createUserWithEmailAndPassword, signInWithEmailAndPassword, onAuthStateChanged, signOut } from "https://www.gstatic.com/firebasejs/12.13.0/firebase-auth.js";
import { getFirestore, collection, addDoc, query, orderBy, onSnapshot, deleteDoc, getDocs } from "https://www.gstatic.com/firebasejs/12.13.0/firebase-firestore.js";

/* FIREBASE CONFIG */
const firebaseConfig = {
  apiKey: "AIzaSyC8HC_0siSrRR4HbrZyDRJGjjKm5v_TyVQ",
  authDomain: "reseau-social-c3205.firebaseapp.com",
  projectId: "reseau-social-c3205",
  storageBucket: "reseau-social-c3205.firebasestorage.app",
  messagingSenderId: "490606823981",
  appId: "1:490606823981:web:0490a8fb9fc5c03d79c6a4"
};

const app = initializeApp(firebaseConfig);
const auth = getAuth(app);
const db = getFirestore(app);

/* ADMIN EMAIL (TOI SEUL) */
const ADMIN_EMAIL = "admin@gmail.com";

/* AUTH */
document.getElementById("register").onclick = () => {
  createUserWithEmailAndPassword(auth, email.value, password.value);
};

document.getElementById("login").onclick = () => {
  signInWithEmailAndPassword(auth, email.value, password.value);
};

/* STATE */
let currentUser = null;

/* LOGIN STATE */
onAuthStateChanged(auth, (user) => {
  if (user) {
    currentUser = user.email;

    document.getElementById("app").style.display = "block";
    welcome.innerText = "Bienvenue " + user.email;

    if (user.email === ADMIN_EMAIL) {
      adminPanel.style.display = "block";
    }

    loadPosts();
    loadMsgs();
  }
});

/* LOGOUT */
window.logout = () => signOut(auth);

/* POSTS */
window.createPost = async () => {
  await addDoc(collection(db, "posts"), {
    user: currentUser,
    text: text.value,
    time: Date.now()
  });
  text.value = "";
};

function loadPosts() {
  const q = query(collection(db, "posts"), orderBy("time", "desc"));

  onSnapshot(q, snap => {
    posts.innerHTML = "";

    snap.forEach(doc => {
      let p = doc.data();

      posts.innerHTML += `
        <div class="post">
          <div class="name">${p.user}</div>
          <div>${p.text}</div>
        </div>
      `;
    });
  });
}

/* MESSAGES */
window.sendMsg = async () => {
  await addDoc(collection(db, "messages"), {
    from: currentUser,
    to: toUser.value,
    text: msgText.value
  });

  msgText.value = "";
};

function loadMsgs() {
  const q = query(collection(db, "messages"));

  onSnapshot(q, snap => {
    msgs.innerHTML = "";

    snap.forEach(doc => {
      let m = doc.data();

      msgs.innerHTML += `<div><b>${m.from}</b> → ${m.to}: ${m.text}</div>`;
    });
  });
}

/* IA SIMPLE */
window.askAI = () => {
  let q = iaInput.value.toLowerCase();
  let r = "Je réfléchis...";

  if (q.includes("bonjour")) r = "Salut 👋";
  else if (q.includes("qui")) r = "Je suis ton assistant IA 🤖";
  else if (q.includes("post")) r = "Tu peux publier ici";

  chat.innerHTML += `<div><b>Toi:</b> ${iaInput.value}</div>`;
  chat.innerHTML += `<div><b>IA:</b> ${r}</div>`;
  iaInput.value = "";
};

/* ADMIN ACTIONS */
window.deleteAllPosts = async () => {
  const snap = await getDocs(collection(db, "posts"));
  snap.forEach(doc => deleteDoc(doc.ref));
};

window.deleteAllMsgs = async () => {
  const snap = await getDocs(collection(db, "messages"));
  snap.forEach(doc => deleteDoc(doc.ref));
};

window.banAll = () => {
  alert("Mode admin activé (simulation)");
};

</script>

</body>
</html>