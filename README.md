<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>ビンゴカード</title>
  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link href="https://fonts.googleapis.com/css2?family=Boogaloo&family=Nunito:wght@400;700;900&display=swap" rel="stylesheet" />
  <style>
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

    body {
      background: #0d1b2a;
      min-height: 100vh;
      display: flex;
      align-items: center;
      justify-content: center;
      font-family: 'Nunito', sans-serif;
    }

    .bg-glow {
      position: fixed;
      inset: 0;
      background: radial-gradient(ellipse at 50% 0%, #1a3a5c 0%, #0d1b2a 70%);
      z-index: 0;
      pointer-events: none;
    }

    .app {
      position: relative;
      z-index: 1;
      width: 100%;
      max-width: 420px;
      padding: 24px 16px 40px;
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 0;
    }

    /* タイトル */
    .header {
      text-align: center;
      margin-bottom: 12px;
    }

    .header-title {
      font-family: 'Boogaloo', cursive;
      font-size: clamp(3rem, 18vw, 5rem);
      letter-spacing: 0.18em;
      line-height: 1;
    }
    .header-title .b1 { color: #ef5350; }
    .header-title .b2 { color: #FFA726; }
    .header-title .b3 { color: #66BB6A; }
    .header-title .b4 { color: #42A5F5; }
    .header-title .b5 { color: #AB47BC; }

    /* ステータス */
    .status-strip {
      width: 100%;
      min-height: 54px;
      border-radius: 12px;
      display: flex;
      align-items: center;
      justify-content: center;
      font-family: 'Boogaloo', cursive;
      font-size: clamp(1.4rem, 6vw, 2rem);
      letter-spacing: 0.12em;
      margin-bottom: 10px;
      transition: background 0.3s, border-color 0.3s;
    }
    .status-waiting {
      background: #1a3652;
      color: #546e7a;
      border: 2px solid #1e4976;
    }
    .status-active {
      background: #1a3652;
      color: #78909c;
      border: 2px solid #1e4976;
      font-size: clamp(1rem, 4vw, 1.3rem);
    }
    .status-bingo {
      background: linear-gradient(135deg, #f9a825, #ff6f00, #e53935);
      color: #fff;
      border: 2px solid #FFD54F;
      animation: pulseGlow 1.2s ease-in-out infinite alternate;
    }
    @keyframes pulseGlow {
      from { box-shadow: 0 0 16px #FFD54F88, 0 0 40px #ff8f0044; }
      to   { box-shadow: 0 0 36px #FFD54Fff, 0 0 80px #ff8f0088; }
    }

    /* 列ラベル */
    .grid-header {
      display: grid;
      grid-template-columns: repeat(5, 1fr);
      gap: 4px;
      width: 100%;
      margin-bottom: 4px;
    }
    .col-label {
      text-align: center;
      font-family: 'Boogaloo', cursive;
      font-size: clamp(1.2rem, 5vw, 1.6rem);
      padding: 4px 0;
    }
    .col-label:nth-child(1) { color: #ef5350; }
    .col-label:nth-child(2) { color: #FFA726; }
    .col-label:nth-child(3) { color: #66BB6A; }
    .col-label:nth-child(4) { color: #42A5F5; }
    .col-label:nth-child(5) { color: #AB47BC; }

    /* グリッド */
    .grid {
      display: grid;
      grid-template-columns: repeat(5, 1fr);
      gap: 4px;
      width: 100%;
    }

    .cell {
      aspect-ratio: 1;
      border-radius: 10px;
      display: flex;
      align-items: center;
      justify-content: center;
      font-family: 'Boogaloo', cursive;
      font-size: clamp(1.1rem, 5vw, 1.6rem);
      cursor: pointer;
      user-select: none;
      transition: transform 0.12s, background 0.2s, border-color 0.2s;
      border: 2px solid transparent;
    }
    .cell-empty {
      background: #1c2f45;
      color: #37474f;
      border-color: #1e3a52;
      cursor: default;
    }
    .cell-unset {
      background: #1a3652;
      color: #90a4ae;
      border-color: #1e4976;
    }
    .cell-unset:hover {
      transform: scale(1.06);
      border-color: #4fc3f7;
      color: #e0f7fa;
    }
    .cell-unset:active { transform: scale(0.93); }
    .cell-hit {
      background: linear-gradient(135deg, #0288d1, #00acc1);
      color: #fff;
      border-color: #4fc3f7;
      box-shadow: 0 0 14px #4fc3f766;
    }
    .cell-hit:active { transform: scale(0.93); }
    .cell-free {
      background: linear-gradient(135deg, #f9a825, #ff8f00);
      color: #fff;
      border-color: #FFD54F;
      box-shadow: 0 0 14px #FFD54F66;
      font-size: clamp(0.65rem, 2.8vw, 0.9rem);
      font-family: 'Nunito', sans-serif;
      font-weight: 900;
      cursor: default;
      letter-spacing: 0.04em;
    }
    .cell-bingo {
      background: linear-gradient(135deg, #ff6f00, #e53935);
      color: #fff;
      border-color: #FFD54F;
      box-shadow: 0 0 18px #FFD54F88;
    }

    /* ボタン */
    .assign-btn {
      width: 100%;
      margin-top: 12px;
      padding: 14px;
      border-radius: 12px;
      border: none;
      background: linear-gradient(135deg, #1565c0, #0288d1);
      color: #fff;
      font-family: 'Boogaloo', cursive;
      font-size: clamp(1.1rem, 5vw, 1.4rem);
      letter-spacing: 0.1em;
      cursor: pointer;
      transition: transform 0.12s, box-shadow 0.12s;
      box-shadow: 0 4px 16px #0288d166;
    }
    .assign-btn:hover {
      transform: translateY(-2px);
      box-shadow: 0 6px 24px #4fc3f788;
    }
    .assign-btn:active { transform: scale(0.97); }

    /* ヒント */
    .hint {
      color: #546e7a;
      font-size: 0.78rem;
      text-align: center;
      letter-spacing: 0.04em;
      line-height: 1.6;
      margin-top: 10px;
    }

    /* 紙吹雪 */
    .confetti-wrap {
      position: fixed;
      inset: 0;
      pointer-events: none;
      z-index: 50;
      overflow: hidden;
    }
    .confetti-piece {
      position: absolute;
      width: 9px;
      height: 9px;
      border-radius: 2px;
      animation: confettiFall linear forwards;
    }
    @keyframes confettiFall {
      0%   { transform: translateY(-20px) rotate(0deg);   opacity: 1; }
      100% { transform: translateY(105vh) rotate(540deg); opacity: 0; }
    }

    /* ビンゴバースト */
    .burst-overlay {
      position: fixed;
      inset: 0;
      display: flex;
      align-items: center;
      justify-content: center;
      z-index: 100;
      pointer-events: none;
    }
    .burst-ring {
      position: absolute;
      width: 280px;
      height: 280px;
      border-radius: 50%;
      border: 8px solid #FFD700;
      animation: ringExpand 0.7s ease-out forwards;
    }
    @keyframes ringExpand {
      from { transform: scale(0.2); opacity: 1; }
      to   { transform: scale(3);   opacity: 0; }
    }
    .burst-text {
      font-family: 'Boogaloo', cursive;
      font-size: clamp(2.5rem, 14vw, 5rem);
      color: #FFD700;
      text-shadow: 0 0 30px #FFD700, 0 0 60px #ff8f00, 0 4px 0 #b8860b;
      letter-spacing: 0.05em;
      text-align: center;
      z-index: 1;
      animation: textPop 0.5s cubic-bezier(0.34,1.56,0.64,1) forwards;
    }
    @keyframes textPop {
      from { transform: scale(0.3); opacity: 0; }
      to   { transform: scale(1);   opacity: 1; }
    }

    /* 画面揺れ */
    .shake { animation: shakeX 0.5s ease-in-out; }
    @keyframes shakeX {
      0%,100% { transform: translateX(0); }
      20%     { transform: translateX(-8px); }
      40%     { transform: translateX(8px); }
      60%     { transform: translateX(-5px); }
      80%     { transform: translateX(5px); }
    }
  </style>
</head>
<body>

<div class="bg-glow"></div>
<div class="confetti-wrap" id="confettiWrap"></div>

<div class="app" id="app">
  <div class="header">
    <div class="header-title">
      <span class="b1">B</span><span class="b2">I</span><span class="b3">N</span><span class="b4">G</span><span class="b5">O</span>
    </div>
  </div>

  <div class="status-strip status-waiting" id="statusStrip">
    数字を割り振ってスタート
  </div>

  <div class="grid-header">
    <div class="col-label">B</div>
    <div class="col-label">I</div>
    <div class="col-label">N</div>
    <div class="col-label">G</div>
    <div class="col-label">O</div>
  </div>

  <div class="grid" id="grid"></div>

  <button class="assign-btn" id="assignBtn" onclick="assignNumbers()">
    🎲 数字を割り振る
  </button>

  <p class="hint" id="hint">ボタンを押してビンゴカードの数字を決めよう</p>
</div>

<script>
  const SIZE = 5;
  const CENTER = 2;

  let grid = null;
  let prevBingoCount = 0;
  let bingoCount = 0;

  function assignNumbers() {
    const nums = new Set();
    while (nums.size < SIZE * SIZE - 1) {
      nums.add(Math.floor(Math.random() * 99) + 1);
    }
    const arr = [...nums];
    let idx = 0;
    grid = [];
    for (let r = 0; r < SIZE; r++) {
      grid[r] = [];
      for (let c = 0; c < SIZE; c++) {
        if (r === CENTER && c === CENTER) {
          grid[r][c] = { num: null, hit: true, free: true };
        } else {
          grid[r][c] = { num: arr[idx++], hit: false, free: false };
        }
      }
    }
    prevBingoCount = 0;
    bingoCount = 0;
    document.getElementById('assignBtn').textContent = '🔀 数字を振り直す';
    document.getElementById('hint').textContent = '司会者が読んだ数字をタップしよう。真ん中は FREE！';
    renderGrid();
    updateStatus();
  }

  function tapCell(r, c) {
    if (!grid) return;
    const cell = grid[r][c];
    if (cell.free) return;
    grid[r][c].hit = !grid[r][c].hit;
    bingoCount = countBingos();
    if (bingoCount > prevBingoCount) {
      prevBingoCount = bingoCount;
      showBurst();
      fireConfetti();
      shakeApp();
    }
    renderGrid();
    updateStatus();
  }

  function getLines() {
    const lines = [];
    for (let r = 0; r < SIZE; r++) {
      lines.push([[r,0],[r,1],[r,2],[r,3],[r,4]]);
    }
    for (let c = 0; c < SIZE; c++) {
      lines.push([[0,c],[1,c],[2,c],[3,c],[4,c]]);
    }
    lines.push([[0,0],[1,1],[2,2],[3,3],[4,4]]);
    lines.push([[0,4],[1,3],[2,2],[3,1],[4,0]]);
    return lines;
  }

  function countBingos() {
    if (!grid) return 0;
    return getLines().filter(line => line.every(([r,c]) => grid[r][c].hit)).length;
  }

  function getBingoCells() {
    const set = new Set();
    getLines().forEach(line => {
      if (line.every(([r,c]) => grid[r][c].hit)) {
        line.forEach(([r,c]) => set.add(r + '-' + c));
      }
    });
    return set;
  }

  function renderGrid() {
    const el = document.getElementById('grid');
    el.innerHTML = '';
    const bingoCells = grid ? getBingoCells() : new Set();

    for (let r = 0; r < SIZE; r++) {
      for (let c = 0; c < SIZE; c++) {
        const div = document.createElement('div');
        div.className = 'cell';

        if (!grid) {
          div.classList.add('cell-empty');
          div.textContent = '—';
        } else {
          const cell = grid[r][c];
          if (cell.free) {
            div.classList.add('cell-free');
            div.textContent = 'FREE';
          } else if (bingoCells.has(r + '-' + c)) {
            div.classList.add('cell-bingo');
            div.textContent = cell.num;
            div.onclick = () => tapCell(r, c);
          } else if (cell.hit) {
            div.classList.add('cell-hit');
            div.textContent = cell.num;
            div.onclick = () => tapCell(r, c);
          } else {
            div.classList.add('cell-unset');
            div.textContent = cell.num;
            div.onclick = () => tapCell(r, c);
          }
        }
        el.appendChild(div);
      }
    }
  }

  function updateStatus() {
    const el = document.getElementById('statusStrip');
    if (!grid) {
      el.className = 'status-strip status-waiting';
      el.textContent = '数字を割り振ってスタート';
    } else if (bingoCount > 0) {
      el.className = 'status-strip status-bingo';
      el.textContent = bingoCount === 1 ? '🎉 BINGO!!!' : '🎉 BINGO × ' + bingoCount;
    } else {
      el.className = 'status-strip status-active';
      el.textContent = '✦ 司会者が読んだ数字をタップ';
    }
  }

  function showBurst() {
    const overlay = document.createElement('div');
    overlay.className = 'burst-overlay';
    const ring = document.createElement('div');
    ring.className = 'burst-ring';
    const text = document.createElement('div');
    text.className = 'burst-text';
    text.textContent = bingoCount === 1 ? 'BINGO!!!' : 'BINGO × ' + bingoCount;
    overlay.appendChild(ring);
    overlay.appendChild(text);
    document.body.appendChild(overlay);
    setTimeout(() => overlay.remove(), 1500);
  }

  function fireConfetti() {
    const wrap = document.getElementById('confettiWrap');
    wrap.innerHTML = '';
    const colors = ['#FFD700','#ef5350','#66BB6A','#42A5F5','#AB47BC','#FFA726'];
    for (let i = 0; i < 35; i++) {
      const d = document.createElement('div');
      d.className = 'confetti-piece';
      const size = 6 + Math.random() * 8;
      d.style.cssText =
        'left:' + (Math.random() * 100) + '%;' +
        'background:' + colors[i % colors.length] + ';' +
        'animation-duration:' + (1.0 + Math.random() * 1.0) + 's;' +
        'animation-delay:' + (Math.random() * 0.5) + 's;' +
        'width:' + size + 'px;height:' + size + 'px;';
      wrap.appendChild(d);
    }
    setTimeout(() => { wrap.innerHTML = ''; }, 2500);
  }

  function shakeApp() {
    const app = document.getElementById('app');
    app.classList.add('shake');
    setTimeout(() => app.classList.remove('shake'), 600);
  }

  renderGrid();
</script>
</body>
</html>
