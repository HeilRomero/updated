// Storage keys
const ATT_KEY  = 'attendance';
const REC_KEY  = 'recitation';

// Load data
let attendance = JSON.parse(localStorage.getItem(ATT_KEY))  || {};
let recitation = JSON.parse(localStorage.getItem(REC_KEY))  || {};

// Counters (only for display – not saved)
let attCount = Object.keys(attendance).length;

// DOM
const attTbody     = document.querySelector('#attendanceTable tbody');
const recTbody     = document.querySelector('#recitationTable tbody');
const resultDiv    = document.getElementById('scanResult');
const nameInput    = document.getElementById('studentName');
const qrBox        = document.getElementById('imgBox');
const qrImg        = document.getElementById('qrImage');
const resetBtn     = document.getElementById('resetBtn');

// Populate on load
function loadTables() {
  attTbody.innerHTML = '';
  recTbody.innerHTML = '';

  // Attendance (present students)
  Object.entries(attendance).forEach(([name, {date, time}], i) => {
    attTbody.insertAdjacentHTML('beforeend', `
      <tr>
        <td>${i+1}</td>
        <td><strong>${name}</strong></td>
        <td>${time}</td>
      </tr>
    `);
  });

  // Recitation (all who attended + status)
  Object.keys(attendance).forEach(name => {
    const recited = !!recitation[name];
    recTbody.insertAdjacentHTML('beforeend', `
      <tr>
        <td>${recTbody.children.length + 1}</td>
        <td><strong>${name}</strong></td>
        <td class="${recited ? 'recited' : 'pending'}">
          ${recited ? 'Recited' : 'Pending'}
        </td>
        <td>
          ${recited ? '' : `<button class="mark-btn" onclick="markRecited('${name}')">Recited</button>`}
        </td>
      </tr>
    `);
  });
}

function markRecited(name) {
  recitation[name] = true;
  localStorage.setItem(REC_KEY, JSON.stringify(recitation));
  loadTables();
}

// QR generate
function generateQR() {
  const name = nameInput.value.trim();
  if (!name) return alert('Please enter your name');

  const id   = Date.now().toString(36) + Math.random().toString(36).slice(2,7);
  const data = `${name}|${id}`;
  const url  = `https://api.qrserver.com/v1/create-qr-code/?size=260x260&data=${encodeURIComponent(data)}`;

  qrImg.src = url;
  qrBox.style.display = 'block';
}

// Enter key support
nameInput.addEventListener('keypress', e => {
  if (e.key === 'Enter') generateQR();
});

// Scanner
const scanner = new Html5QrcodeScanner('reader', { fps: 12, qrbox: 240 });

function onScanSuccess(text) {
  try {
    const [name] = text.split('|');
    if (!name) throw new Error();

    if (attendance[name]) {
      resultDiv.className = 'result-box already';
      resultDiv.textContent = `Already present: ${name}`;
      beep(400, 0.2);
      setTimeout(() => resultDiv.textContent = '', 2200);
      return;
    }

    const now = new Date();
    const timeStr = now.toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'});

    attendance[name] = { date: now.toLocaleDateString(), time: timeStr };
    localStorage.setItem(ATT_KEY, JSON.stringify(attendance));

    resultDiv.className = 'result-box success';
    resultDiv.textContent = `Marked present: ${name}`;
    beep(900, 0.15);

    loadTables();

    setTimeout(() => resultDiv.textContent = '', 3000);
  } catch {
    resultDiv.className = 'result-box error';
    resultDiv.textContent = 'Invalid QR code';
    setTimeout(() => resultDiv.textContent = '', 2200);
  }
}

function beep(freq = 700, dur = 0.1) {
  const ctx = new (window.AudioContext || window.webkitAudioContext)();
  const osc = ctx.createOscillator();
  const gain = ctx.createGain();
  osc.connect(gain); gain.connect(ctx.destination);
  osc.frequency.value = freq;
  osc.type = 'square';
  gain.gain.value = 0.12;
  osc.start();
  osc.stop(ctx.currentTime + dur);
}

scanner.render(onScanSuccess);

// Reset everything for today
resetBtn.onclick = () => {
  if (!confirm('Reset all attendance and recitation for today?')) return;
  attendance = {};
  recitation = {};
  localStorage.removeItem(ATT_KEY);
  localStorage.removeItem(REC_KEY);
  loadTables();
  resultDiv.textContent = 'Session reset.';
  setTimeout(() => resultDiv.textContent = '', 3000);
};

// Initial load
loadTables();
