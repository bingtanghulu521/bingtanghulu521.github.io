# bingtanghulu521.github.io
<!DOCTYPE html>
<html lang="zh-TW">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>抽獎系統</title>
<link href="https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@400;700;900&family=Playfair+Display:wght@700&display=swap" rel="stylesheet">
<style>
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  :root {
    --gold: #f5c842;
    --gold-dim: #c9962a;
    --bg: #0d0d1a;
    --surface: #16162a;
    --surface2: #1e1e35;
    --text: #f0eada;
    --text-muted: #888;
    --red: #e84545;
  }

  body {
    font-family: 'Noto Sans TC', sans-serif;
    background: var(--bg);
    color: var(--text);
    min-height: 100vh;
    display: flex;
    flex-direction: column;
    align-items: center;
    padding: 2rem 1rem 4rem;
    overflow-x: hidden;
  }

  /* Starfield bg */
  body::before {
    content: '';
    position: fixed;
    inset: 0;
    background-image:
      radial-gradient(1px 1px at 10% 20%, rgba(255,255,255,0.4) 0%, transparent 100%),
      radial-gradient(1px 1px at 30% 60%, rgba(255,255,255,0.3) 0%, transparent 100%),
      radial-gradient(1px 1px at 55% 15%, rgba(255,255,255,0.5) 0%, transparent 100%),
      radial-gradient(1px 1px at 70% 80%, rgba(255,255,255,0.3) 0%, transparent 100%),
      radial-gradient(1px 1px at 85% 35%, rgba(255,255,255,0.4) 0%, transparent 100%),
      radial-gradient(1px 1px at 20% 85%, rgba(255,255,255,0.3) 0%, transparent 100%),
      radial-gradient(1px 1px at 90% 70%, rgba(255,255,255,0.5) 0%, transparent 100%),
      radial-gradient(1px 1px at 45% 45%, rgba(255,255,255,0.2) 0%, transparent 100%);
    pointer-events: none;
    z-index: 0;
  }

  .wrapper {
    position: relative;
    z-index: 1;
    width: 100%;
    max-width: 560px;
  }

  header {
    text-align: center;
    margin-bottom: 2.5rem;
  }

  .crown {
    font-size: 3rem;
    animation: float 3s ease-in-out infinite;
  }

  @keyframes float {
    0%, 100% { transform: translateY(0); }
    50% { transform: translateY(-8px); }
  }

  h1 {
    font-family: 'Playfair Display', serif;
    font-size: 2.4rem;
    color: var(--gold);
    letter-spacing: 0.04em;
    text-shadow: 0 0 40px rgba(245, 200, 66, 0.3);
    margin-top: 0.3rem;
  }

  .subtitle {
    font-size: 0.85rem;
    color: var(--text-muted);
    margin-top: 0.4rem;
    letter-spacing: 0.1em;
  }

  .card {
    background: var(--surface);
    border: 1px solid rgba(245, 200, 66, 0.15);
    border-radius: 20px;
    padding: 1.5rem;
    margin-bottom: 1rem;
  }

  label {
    display: block;
    font-size: 0.78rem;
    letter-spacing: 0.12em;
    color: var(--gold-dim);
    text-transform: uppercase;
    margin-bottom: 0.6rem;
    font-weight: 700;
  }

  textarea {
    width: 100%;
    height: 160px;
    background: var(--surface2);
    border: 1px solid rgba(255,255,255,0.08);
    border-radius: 12px;
    color: var(--text);
    font-family: 'Noto Sans TC', sans-serif;
    font-size: 0.95rem;
    padding: 0.8rem 1rem;
    resize: vertical;
    outline: none;
    transition: border-color 0.2s;
    line-height: 1.8;
  }

  textarea:focus {
    border-color: rgba(245, 200, 66, 0.4);
  }

  .count-row {
    display: flex;
    align-items: center;
    gap: 1rem;
  }

  .count-row input[type=number] {
    width: 90px;
    background: var(--surface2);
    border: 1px solid rgba(255,255,255,0.08);
    border-radius: 10px;
    color: var(--text);
    font-family: 'Noto Sans TC', sans-serif;
    font-size: 1.2rem;
    font-weight: 700;
    padding: 0.5rem 0.8rem;
    outline: none;
    text-align: center;
    transition: border-color 0.2s;
  }

  .count-row input[type=number]:focus {
    border-color: rgba(245, 200, 66, 0.4);
  }

  .count-hint {
    font-size: 0.82rem;
    color: var(--text-muted);
  }

  .btn-draw {
    width: 100%;
    padding: 1rem;
    background: linear-gradient(135deg, #f5c842, #e8a020);
    border: none;
    border-radius: 14px;
    color: #1a1000;
    font-family: 'Noto Sans TC', sans-serif;
    font-size: 1.15rem;
    font-weight: 900;
    letter-spacing: 0.08em;
    cursor: pointer;
    transition: transform 0.15s, box-shadow 0.15s, opacity 0.2s;
    box-shadow: 0 4px 24px rgba(245, 200, 66, 0.25);
    position: relative;
    overflow: hidden;
  }

  .btn-draw::after {
    content: '';
    position: absolute;
    inset: 0;
    background: white;
    opacity: 0;
    transition: opacity 0.15s;
  }

  .btn-draw:hover:not(:disabled) {
    transform: translateY(-2px);
    box-shadow: 0 8px 32px rgba(245, 200, 66, 0.4);
  }

  .btn-draw:active:not(:disabled) {
    transform: scale(0.98);
  }

  .btn-draw:disabled {
    opacity: 0.5;
    cursor: not-allowed;
  }

  /* Slot machine display */
  .slot-area {
    display: none;
    background: var(--surface);
    border: 1px solid rgba(245, 200, 66, 0.2);
    border-radius: 20px;
    padding: 1.5rem;
    margin-bottom: 1rem;
    text-align: center;
  }

  .slot-area.visible { display: block; }

  .slot-label {
    font-size: 0.78rem;
    letter-spacing: 0.12em;
    color: var(--gold-dim);
    text-transform: uppercase;
    font-weight: 700;
    margin-bottom: 1rem;
  }

  .slot-display {
    background: var(--surface2);
    border: 2px solid rgba(245, 200, 66, 0.3);
    border-radius: 14px;
    padding: 1rem;
    min-height: 64px;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 1.6rem;
    font-weight: 900;
    color: var(--gold);
    letter-spacing: 0.04em;
    overflow: hidden;
    position: relative;
    text-shadow: 0 0 20px rgba(245,200,66,0.4);
  }

  .slot-name {
    transition: none;
  }

  .slot-name.rolling {
    animation: slotRoll 0.06s linear infinite;
  }

  @keyframes slotRoll {
    0%   { transform: translateY(0);    opacity: 1; }
    49%  { transform: translateY(-8px); opacity: 0; }
    50%  { transform: translateY(8px);  opacity: 0; }
    100% { transform: translateY(0);    opacity: 1; }
  }

  /* Winner list */
  .result-area {
    display: none;
    background: var(--surface);
    border: 1px solid rgba(245, 200, 66, 0.2);
    border-radius: 20px;
    padding: 1.5rem;
    margin-bottom: 1rem;
  }

  .result-area.visible { display: block; }

  .result-label {
    font-size: 0.78rem;
    letter-spacing: 0.12em;
    color: var(--gold-dim);
    text-transform: uppercase;
    font-weight: 700;
    margin-bottom: 1rem;
    text-align: center;
  }

  .winner-list {
    display: flex;
    flex-direction: column;
    gap: 0.6rem;
  }

  .winner-item {
    display: flex;
    align-items: center;
    gap: 0.8rem;
    background: var(--surface2);
    border: 1px solid rgba(245, 200, 66, 0.15);
    border-radius: 12px;
    padding: 0.75rem 1rem;
    opacity: 0;
    transform: translateY(12px);
    animation: winnerIn 0.4s forwards;
  }

  @keyframes winnerIn {
    to { opacity: 1; transform: translateY(0); }
  }

  .winner-rank {
    width: 28px;
    height: 28px;
    border-radius: 50%;
    background: linear-gradient(135deg, #f5c842, #e8a020);
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 0.8rem;
    font-weight: 900;
    color: #1a1000;
    flex-shrink: 0;
  }

  .winner-name {
    font-size: 1.1rem;
    font-weight: 700;
    color: var(--text);
  }

  .btn-again {
    width: 100%;
    padding: 0.8rem;
    background: transparent;
    border: 1px solid rgba(245, 200, 66, 0.4);
    border-radius: 14px;
    color: var(--gold);
    font-family: 'Noto Sans TC', sans-serif;
    font-size: 0.95rem;
    font-weight: 700;
    letter-spacing: 0.06em;
    cursor: pointer;
    transition: background 0.2s, transform 0.15s;
    margin-top: 1rem;
  }

  .btn-again:hover {
    background: rgba(245, 200, 66, 0.08);
    transform: translateY(-1px);
  }

  /* Confetti */
  #confetti-canvas {
    position: fixed;
    inset: 0;
    pointer-events: none;
    z-index: 999;
  }
</style>
</head>
<body>
<canvas id="confetti-canvas"></canvas>

<div class="wrapper">
  <header>
    <div class="crown">🎰</div>
    <h1>幸運抽獎</h1>
    <p class="subtitle">LUCKY DRAW SYSTEM</p>
  </header>

  <div class="card">
    <label>參加名單（每行一個名字）</label>
    <textarea id="nameList" placeholder="吳宗祐&#10;王振仰&#10;胡立安"></textarea>
  </div>

  <div class="card">
    <label>抽出人數</label>
    <div class="count-row">
      <input type="number" id="winnerCount" value="1" min="1">
      <span class="count-hint">位幸運得獎者</span>
    </div>
  </div>

  <button class="btn-draw" id="drawBtn" onclick="startDraw()">🎲 開始抽獎</button>

  <div class="slot-area" id="slotArea">
    <div class="slot-label">⚡ 抽選中…</div>
    <div class="slot-display">
      <span class="slot-name" id="slotName"></span>
    </div>
  </div>

  <div class="result-area" id="resultArea">
    <div class="result-label">🎉 中獎名單</div>
    <div class="winner-list" id="winnerList"></div>
    <button class="btn-again" onclick="resetDraw()">↩ 再抽一次</button>
  </div>
</div>

<script>
const ROLL_DURATION = 1600;
const ROLL_INTERVAL = 60;

function startDraw() {
  let names = document.getElementById('nameList').value
    .split('\n')
    .map(n => n.trim())
    .filter(n => n !== '');
  let count = parseInt(document.getElementById('winnerCount').value);

  if (names.length === 0) { alert('請輸入名單！'); return; }
  if (count > names.length) { alert('抽出人數不能大於名單人數！'); return; }

  const btn = document.getElementById('drawBtn');
  btn.disabled = true;

  document.getElementById('resultArea').classList.remove('visible');
  document.getElementById('slotArea').classList.add('visible');

  let pool = [...names];
  let winners = [];

  function drawOne(i) {
    if (i >= count) {
      showWinners(winners);
      return;
    }

    const slotName = document.getElementById('slotName');
    slotName.classList.add('rolling');

    let rollTimer = setInterval(() => {
      slotName.textContent = pool[Math.floor(Math.random() * pool.length)];
    }, ROLL_INTERVAL);

    setTimeout(() => {
      clearInterval(rollTimer);
      slotName.classList.remove('rolling');
      let idx = Math.floor(Math.random() * pool.length);
      let winner = pool[idx];
      pool.splice(idx, 1);
      winners.push(winner);
      slotName.textContent = winner;

      setTimeout(() => drawOne(i + 1), 600);
    }, ROLL_DURATION);
  }

  drawOne(0);
}

function showWinners(winners) {
  document.getElementById('slotArea').classList.remove('visible');
  const list = document.getElementById('winnerList');
  list.innerHTML = '';

  winners.forEach((name, i) => {
    const item = document.createElement('div');
    item.className = 'winner-item';
    item.style.animationDelay = (i * 0.12) + 's';
    item.innerHTML = `<div class="winner-rank">${i + 1}</div><div class="winner-name">${name}</div>`;
    list.appendChild(item);
  });

  document.getElementById('resultArea').classList.add('visible');
  document.getElementById('drawBtn').disabled = false;
  launchConfetti();
}

function resetDraw() {
  document.getElementById('resultArea').classList.remove('visible');
  document.getElementById('slotArea').classList.remove('visible');
}

/* Confetti */
function launchConfetti() {
  const canvas = document.getElementById('confetti-canvas');
  const ctx = canvas.getContext('2d');
  canvas.width = window.innerWidth;
  canvas.height = window.innerHeight;

  const colors = ['#f5c842','#e84545','#4fc3f7','#81c784','#ce93d8','#ff8a65'];
  const particles = Array.from({ length: 120 }, () => ({
    x: Math.random() * canvas.width,
    y: -10,
    r: Math.random() * 6 + 3,
    d: Math.random() * 120 + 60,
    color: colors[Math.floor(Math.random() * colors.length)],
    vx: (Math.random() - 0.5) * 4,
    vy: Math.random() * 3 + 2,
    angle: Math.random() * 360,
    spin: (Math.random() - 0.5) * 6,
    shape: Math.random() > 0.5 ? 'rect' : 'circle',
  }));

  let frame;
  let startTime = Date.now();

  function draw() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    const elapsed = Date.now() - startTime;

    particles.forEach(p => {
      ctx.save();
      ctx.translate(p.x, p.y);
      ctx.rotate((p.angle * Math.PI) / 180);
      ctx.fillStyle = p.color;
      ctx.globalAlpha = Math.max(0, 1 - elapsed / 3000);
      if (p.shape === 'rect') {
        ctx.fillRect(-p.r, -p.r / 2, p.r * 2, p.r);
      } else {
        ctx.beginPath();
        ctx.arc(0, 0, p.r, 0, Math.PI * 2);
        ctx.fill();
      }
      ctx.restore();

      p.x += p.vx;
      p.y += p.vy;
      p.angle += p.spin;
      p.vy += 0.05;
    });

    if (elapsed < 3200) {
      frame = requestAnimationFrame(draw);
    } else {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
    }
  }

  cancelAnimationFrame(frame);
  draw();
}
</script>
</body>
</html>