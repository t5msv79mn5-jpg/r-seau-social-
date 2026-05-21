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