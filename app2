import { initializeApp } from "https://www.gstatic.com/firebasejs/9.23.0/firebase-app.js";
import { getAuth, signInAnonymously } from "https://www.gstatic.com/firebasejs/9.23.0/firebase-auth.js";
import { getDatabase, ref, onValue } from "https://www.gstatic.com/firebasejs/9.23.0/firebase-database.js";

const firebaseConfig = {
  apiKey: "AIzaSyAT6VhvQggviNUxDhL8KQKcyCi_Q1S6gjU",
  authDomain: "capstone3-bc2c3.firebaseapp.com",
  databaseURL: "https://capstone3-bc2c3-default-rtdb.asia-southeast1.firebasedatabase.app",
  projectId: "capstone3-bc2c3",
  storageBucket: "capstone3-bc2c3.firebasestorage.app",
  messagingSenderId: "948536456584",
  appId: "1:948536456584:web:2e47332cbd2729b2c1363d"
};

const app = initializeApp(firebaseConfig);
const auth = getAuth(app);
const db = getDatabase(app);

const statusEl = document.getElementById("status");
const lastRefreshEl = document.getElementById("lastRefresh");
const latestBody = document.getElementById("latestBody");
const searchEl = document.getElementById("search");

function fmtTime(ms) {
  if (!ms) return "-";
  return new Date(ms).toLocaleString();
}

let latestCache = {}; // uidKey -> record

function renderLatest() {
  const q = (searchEl.value || "").trim().toLowerCase();
  latestBody.innerHTML = "";

  const rows = Object.entries(latestCache)
    .map(([uidKey, rec]) => ({ uidKey, ...rec }))
    .sort((a, b) => (b.inspectedAt || 0) - (a.inspectedAt || 0));

  for (const r of rows) {
    const uidKey = r.uidKey || "";
    const name = r.name || "";
    const inspector = r.inspector || "";
    const status = r.status || "";
    const inspectedAt = r.inspectedAt || 0;

    const hay = (uidKey + " " + name).toLowerCase();
    if (q && !hay.includes(q)) continue;

    const tr = document.createElement("tr");
    tr.innerHTML = `
      <td>${uidKey}</td>
      <td>${name}</td>
      <td>${status}</td>
      <td>${inspector}</td>
      <td>${fmtTime(inspectedAt)}</td>
    `;
    latestBody.appendChild(tr);
  }

  lastRefreshEl.textContent = new Date().toLocaleString();
}

searchEl.addEventListener("input", renderLatest);

async function start() {
  try {
    statusEl.textContent = "Signing in (anonymous)...";
    await signInAnonymously(auth);

    statusEl.textContent = "Connected. Listening to latestInspection...";
    const latestRef = ref(db, "latestInspection");

    onValue(latestRef, (snap) => {
      latestCache = snap.val() || {};
      renderLatest();
    });
  } catch (e) {
    console.error(e);
    statusEl.textContent = "Error: " + (e?.message || e);
  }
}

start();
