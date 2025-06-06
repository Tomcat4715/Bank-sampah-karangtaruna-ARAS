<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Dompet Karangtaruna ARAS - Login</title>
  <link rel="stylesheet" href="./style.css" />
</head>
<body>
  <div class="container">
    <h1>Login</h1>
    <input type="text" id="phone" placeholder="Nomor Telepon" />
    <input type="password" id="password" placeholder="Password" />
    <button id="loginBtn">Login</button>
    <p id="error" style="color: red;"></p>
  </div>
  <script type="module" src="./index.js"></script>
</body>
</html>
<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Dashboard</title>
  <link rel="stylesheet" href="./style.css" />
</head>
<body>
  <div class="container">
    <h1>Saldo Anda</h1>
    <p id="saldo">Loading...</p>
    <input type="number" id="jumlah" placeholder="Jumlah" />
    <button id="tambah">Tambah</button>
    <button id="tarik">Tarik</button>
    <button id="logout">Logout</button>
    <p id="status" style="color: green;"></p>
  </div>
  <script type="module" src="./dashboard.js"></script>
</body>
</html>
body {
  font-family: Arial, sans-serif;
  background-color: #f4f4f4;
  margin: 0;
  padding: 0;
  display: flex;
  height: 100vh;
  justify-content: center;
  align-items: center;
}

.container {
  background: white;
  padding: 20px;
  border-radius: 10px;
  width: 90%;
  max-width: 400px;
  box-shadow: 0 0 10px rgba(0,0,0,0.1);
}

input, button {
  display: block;
  width: 100%;
  margin-top: 10px;
  padding: 10px;
  font-size: 16px;
}

button {
  background-color: #007bff;
  color: white;
  border: none;
  cursor: pointer;
}

button:hover {
  background-color: #0056b3;
}
import { initializeApp } from "https://www.gstatic.com/firebasejs/9.23.0/firebase-app.js";
import { getAuth } from "https://www.gstatic.com/firebasejs/9.23.0/firebase-auth.js";
import { getFirestore } from "https://www.gstatic.com/firebasejs/9.23.0/firebase-firestore.js";

const firebaseConfig = {
  apiKey: "AIzaSyDVUHmKIjaHWdoAdB20cmAM06LWdFJBRW8",
  authDomain: "dompet-karangtaruna-aras.firebaseapp.com",
  projectId: "dompet-karangtaruna-aras",
  storageBucket: "dompet-karangtaruna-aras.appspot.com",
  messagingSenderId: "373945571180",
  appId: "1:373945571180:web:28823141c06db4e7d6c549",
  measurementId: "G-PWB8RX7JF0"
};

const app = initializeApp(firebaseConfig);
export const auth = getAuth(app);
export const db = getFirestore(app);
import { auth, db } from './firebase-config.js';
import { signInWithEmailAndPassword } from "https://www.gstatic.com/firebasejs/9.23.0/firebase-auth.js";

document.getElementById('loginBtn').addEventListener('click', async () => {
  const phone = document.getElementById('phone').value;
  const password = document.getElementById('password').value;

  try {
    const email = `${phone}@dompet-aras.com`; // Email hasil dari nomor
    const userCredential = await signInWithEmailAndPassword(auth, email, password);
    localStorage.setItem("uid", userCredential.user.uid);
    window.location.href = "dashboard.html";
  } catch (error) {
    document.getElementById("error").textContent = error.message;
  }
});
import { auth, db } from './firebase-config.js';
import { doc, getDoc, updateDoc } from "https://www.gstatic.com/firebasejs/9.23.0/firebase-firestore.js";
import { signOut } from "https://www.gstatic.com/firebasejs/9.23.0/firebase-auth.js";

const uid = localStorage.getItem("uid");
const userRef = doc(db, "users", uid);

async function updateSaldo() {
  const snap = await getDoc(userRef);
  document.getElementById("saldo").textContent = `Rp ${snap.data().saldo}`;
}

updateSaldo();

async function ubahSaldo(jumlah) {
  const snap = await getDoc(userRef);
  const saldoSekarang = snap.data().saldo;
  await updateDoc(userRef, { saldo: saldoSekarang + jumlah });
  updateSaldo();
  document.getElementById("status").textContent = jumlah > 0 ? "Saldo ditambahkan" : "Saldo ditarik";
}

document.getElementById("tambah").onclick = () => {
  const jml = parseInt(document.getElementById("jumlah").value);
  if (jml > 0) ubahSaldo(jml);
};

document.getElementById("tarik").onclick = () => {
  const jml = parseInt(document.getElementById("jumlah").value);
  if (jml > 0) ubahSaldo(-jml);
};

document.getElementById("logout").onclick = async () => {
  await signOut(auth);
  localStorage.clear();
  window.location.href = "index.html";
};
