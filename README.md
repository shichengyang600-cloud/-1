<!DOCTYPE html>
<html lang="zh-TW">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>療癒系俄羅斯方塊 · Mindful Tetris</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@500;600;700&family=Zen+Maru+Gothic:wght@400;500;700&display=swap" rel="stylesheet">
  <style>
    :root {
      --bg-gradient: linear-gradient(135deg, #fbf7f4 0%, #f0f4f8 50%, #f6f0f8 100%);
      --card-bg: rgba(255, 255, 255, 0.72);
      --card-border: rgba(255, 255, 255, 0.85);
      --card-shadow: 0 20px 45px -12px rgba(100, 116, 139, 0.12), 0 4px 12px -2px rgba(100, 116, 139, 0.04);
      --text-main: #334155;
      --text-muted: #78889b;
      --accent: #88a4bc;
      --accent-soft: #eaf1f7;
      --accent-deep: #476882;
      --panel-bg: rgba(255, 255, 255, 0.6);
      --border-subtle: rgba(226, 232, 240, 0.8);
    }

    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
      font-family: 'Zen Maru Gothic', 'Plus Jakarta Sans', 'PingFang TC', sans-serif;
      -webkit-font-smoothing: antialiased;
    }

    body {
      background: var(--bg-gradient);
      color: var(--text-main);
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
      padding: 30px 16px;
    }

    .main-wrapper {
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 20px;
      width: 100%;
      max-width: 820px;
    }

    .game-header {
      text-align: center;
    }

    .game-header h1 {
      font-size: 1.7rem;
      font-weight: 700;
      letter-spacing: 2px;
      color: #2c3e50;
      display: flex;
      align-items: center;
      justify-content: center;
      gap: 8px;
    }

    .game-header p {
      font-size: 0.88rem;
      color: var(--text-muted);
      margin-top: 4px;
      letter-spacing: 0.5px;
    }

    .container {
      display: flex;
      gap: 28px;
      background: var(--card-bg);
      backdrop-filter: blur(20px);
      -webkit-backdrop-filter: blur(20px);
      padding: 28px;
      border-radius: 28px;
      border: 1px solid var(--card-border);
      box-shadow: var(--card-shadow);
      width: 100%;
      justify-content: center;
      flex-wrap: wrap;
    }

    .game-area {
      position: relative;
      display: flex;
      align-items: center;
      justify-content: center;
    }

    #tetris {
      border: 3px solid rgba(255, 255, 255, 0.9);
      border-radius: 20px;
      background: #fafbfd;
      box-shadow: inset 0 2px 10px rgba(0, 0, 0, 0.03), 0 10px 25px -5px rgba(148, 163, 184, 0.15);
      display: block;
    }

    .sidebar {
      display: flex;
      flex-direction: column;
      gap: 16px;
      width: 270px;
    }

    .panel {
      background: var(--panel-bg);
      backdrop-filter: blur(10px);
      padding: 16px 18px;
      border-radius: 18px;
      border: 1px solid var(--border-subtle);
      box-shadow: 0 4px 16px rgba(0, 0, 0, 0.02);
      transition: transform 0.2s ease;
    }

    .panel-score-next {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 12px;
      padding: 0;
      background: transparent;
      border: none;
      box-shadow: none;
    }

    .panel-score-next .panel {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      padding: 14px;
    }

    h2 {
      font-size: 0.85rem;
      font-weight: 700;
      color: var(--text-muted);
      letter-spacing: 1px;
      text-transform: uppercase;
      margin-bottom: 6px;
    }

    .stat-val {
      font-family: 'Plus Jakarta Sans', sans-serif;
      font-size: 2.2rem;
      font-weight: 700;
      color: var(--accent-deep);
      line-height: 1.1;
      margin-top: 4px;
    }

    #next {
      display: block;
      border-radius: 12px;
      background: rgba(255, 255, 255, 0.5);
    }

    .controls {
      display: flex;
      flex-direction: column;
      gap: 10px;
    }

    button {
      background: #ffffff;
      color: var(--text-main);
      border: 1px solid rgba(226, 232, 240, 0.9);
      padding: 11px 16px;
      border-radius: 14px;
      font-size: 0.92rem;
      font-weight: 500;
      cursor: pointer;
      box-shadow: 0 2px 5px rgba(0, 0, 0, 0.02);
      transition: all 0.22s cubic-bezier(0.16, 1, 0.3, 1);
      display: flex;
      align-items: center;
      justify-content: space-between;
    }

    button:hover {
      background-color: var(--accent-soft);
      border-color: var(--accent);
      color: var(--accent-deep);
      transform: translateY(-1px);
      box-shadow: 0 4px 10px rgba(136, 164, 188, 0.2);
    }

    button:active {
      transform: translateY(0);
    }

    button.active {
      background: var(--accent-deep);
      border-color: var(--accent-deep);
      color: #ffffff;
      box-shadow: 0 4px 12px rgba(71, 104, 130, 0.3);
    }

    .btn-icon {
      font-size: 1.1rem;
      line-height: 1;
    }

    .slider-group {
      margin-top: 6px;
      display: flex;
      flex-direction: column;
      gap: 8px;
    }

    .slider-header {
      display: flex;
      justify-content: space-between;
      font-size: 0.85rem;
      font-weight: 500;
      color: var(--text-muted);
    }

    .slider-header span.val {
      font-family: 'Plus Jakarta Sans', sans-serif;
      font-weight: 700;
      color: var(--accent-deep);
    }

    input[type="range"] {
      -webkit-appearance: none;
      width: 100%;
      height: 6px;
      border-radius: 4px;
      background: #e2e8f0;
      outline: none;
    }

    input[type="range"]::-webkit-slider-thumb {
      -webkit-appearance: none;
      width: 18px;
      height: 18px;
      border-radius: 50%;
      background: var(--accent);
      cursor: pointer;
      box-shadow: 0 2px 6px rgba(136, 164, 188, 0.4);
      transition: transform 0.15s ease;
    }

    input[type="range"]::-webkit-slider-thumb:hover {
      transform: scale(1.2);
      background: var(--accent-deep);
    }

    .key-hints {
      font-size: 0.82rem;
      line-height: 1.8;
      color: var(--text-muted);
    }

    .key-hints strong {
      color: var(--text-main);
      display: block;
      margin-bottom: 4px;
      font-size: 0.85rem;
    }

    .key-hints kbd {
      background: #ffffff;
      border: 1px solid #cbd5e1;
      border-bottom-width: 2px;
      color: #475569;
      padding: 1px 7px;
      border-radius: 6px;
      font-size: 0.76rem;
      font-family: 'Plus Jakarta Sans', monospace;
      font-weight: 600;
      box-shadow: 0 1px 2px rgba(0, 0, 0, 0.05);
    }
  </style>
</head>
<body>

<div class="main-wrapper">
  <div class="game-header">
    <h1>🌱 靜心方塊</h1>
    <p>深呼吸 · 按照自己的節奏安放每一塊形狀</p>
  </div>

  <div class="container">
    <div class="game-area">
      <canvas id="tetris" width="240" height="400"></canvas>
    </div>

    <div class="sidebar">
      <div class="panel-score-next">
        <div class="panel">
          <h2>分數</h2>
          <div id="score" class="stat-val">0</div>
        </div>

        <div class="panel">
          <h2>下一塊</h2>
          <canvas id="next" width="90" height="90"></canvas>
        </div>
      </div>

      <div class="panel controls">
        <h2>療癒手感調節</h2>
        <button id="btn-pause">暫停 (P) <span class="btn-icon">⏸</span></button>
        <button id="btn-undo">回上一步 (Z) <span class="btn-icon">↩</span></button>
        <button id="btn-freeze">靜止漂浮 (F) <span class="btn-icon">☁️</span></button>

        <div class="slider-group">
          <div class="slider-header">
            <span>下落節奏</span>
            <span class="val"><span id="speed-val">1.0</span>x</span>
          </div>
          <input type="range" id="speed-slider" min="0.1" max="2.0" step="0.1" value="1.0">
        </div>
      </div>

      <div class="panel key-hints">
        <strong>放鬆指南：</strong>
        <kbd>←</kbd> <kbd>→</kbd> 平移 · <kbd>↑</kbd> 旋轉<br>
        <kbd>↓</kbd> 緩降 · <kbd>Space</kbd> 瞬落安放<br>
        <kbd>Z</kbd> 悔棋倒帶 · <kbd>F</kbd> 懸停沉思
      </div>
    </div>
  </div>
</div>

<script>
  const canvas = document.getElementById('tetris');
  const context = canvas.getContext('2d');
  const nextCanvas = document.getElementById('next');
  const nextContext = nextCanvas.getContext('2d');

  const BLOCK_SIZE = 20;
  context.scale(BLOCK_SIZE, BLOCK_SIZE);
  nextContext.scale(18, 18);

  const PALETTE = [
    null,
    { main: '#86a8b8', light: '#b3cbda', dark: '#638495' }, // I
    { main: '#9bb8cd', light: '#c5daf0', dark: '#7590a5' }, // J
    { main: '#cfaf9b', light: '#ebd7c9', dark: '#ac8b76' }, // L
    { main: '#e8c977', light: '#fae7aa', dark: '#c2a14e' }, // O
    { main: '#88bea0', light: '#b5dfc6', dark: '#619678' }, // S
    { main: '#b9a0ce', light: '#dccee8', dark: '#947aa9' }, // T
    { main: '#d89b9b', light: '#f2c9c9', dark: '#b06f6f' }, // Z
  ];

  function createMatrix(w, h) {
    const matrix = [];
    while (h--) matrix.push(new Array(w).fill(0));
    return matrix;
  }

  function createPiece(type) {
    if (type === 'I') {
      return [
        [0, 1, 0, 0],
        [0, 1, 0, 0],
        [0, 1, 0, 0],
        [0, 1, 0, 0],
      ];
    } else if (type === 'L') {
      return [
        [0, 2, 0],
        [0, 2, 0],
        [0, 2, 2],
      ];
    } else if (type === 'J') {
      return [
        [0, 3, 0],
        [0, 3, 0],
        [3, 3, 0],
      ];
    } else if (type === 'O') {
      return [
        [4, 4],
        [4, 4],
      ];
    } else if (type === 'Z') {
      return [
        [5, 5, 0],
        [0, 5, 5],
        [0, 0, 0],
      ];
    } else if (type === 'S') {
      return [
        [0, 6, 6],
        [6, 6, 0],
        [0, 0, 0],
      ];
    } else if (type === 'T') {
      return [
        [0, 7, 0],
        [7, 7, 7],
        [0, 0, 0],
      ];
    }
  }

  const arena = createMatrix(12, 20);

  const player = {
    pos: {x: 0, y: 0},
    matrix: null,
    next: null,
    score: 0,
  };

  let isPaused = false;
  let isFrozen = false;
  let baseSpeed = 900;
  let speedMultiplier = 1.0;
  let dropCounter = 0;
  let lastTime = 0;

  let historyStack = [];
  const MAX_HISTORY = 30;

  function saveState() {
    const state = {
      arena: arena.map(row => [...row]),
      playerPos: { ...player.pos },
      playerMatrix: player.matrix ? player.matrix.map(row => [...row]) : null,
      playerNext: player.next,
      score: player.score
    };
    historyStack.push(JSON.stringify(state));
    if (historyStack.length > MAX_HISTORY) {
      historyStack.shift();
    }
  }

  function undo() {
    if (historyStack.length === 0) return;
    const previousState = JSON.parse(historyStack.pop());
    for (let y = 0; y < arena.length; ++y) {
      for (let x = 0; x < arena[y].length; ++x) {
        arena[y][x] = previousState.arena[y][x];
      }
    }
    player.pos = previousState.playerPos;
    player.matrix = previousState.playerMatrix;
    player.next = previousState.playerNext;
    player.score = previousState.score;
    updateScore();
    drawNext();
  }

  function arenaSweep() {
    let rowCount = 1;
    outer: for (let y = arena.length - 1; y >= 0; --y) {
      for (let x = 0; x < arena[y].length; ++x) {
        if (arena[y][x] === 0) continue outer;
      }
      const row = arena.splice(y, 1)[0].fill(0);
      arena.unshift(row);
      ++y;
      player.score += rowCount * 10;
      rowCount *= 2;
    }
    updateScore();
  }

  function collide(arena, player) {
    const [m, o] = [player.matrix, player.pos];
    for (let y = 0; y < m.length; ++y) {
      for (let x = 0; x < m[y].length; ++x) {
        if (m[y][x] !== 0 &&
           (arena[y + o.y] && arena[y + o.y][x + o.x]) !== 0) {
          return true;
        }
      }
    }
    return false;
  }

  function drawRoundedBlock(ctx, x, y, colorIdx, isGhost = false) {
    const palette = PALETTE[colorIdx];
    const pad = 0.08;
    const size = 1 - pad * 2;
    const radius = 0.22;

    ctx.save();
    ctx.translate(x + pad, y + pad);

    ctx.beginPath();
    ctx.roundRect(0, 0, size, size, radius);

    if (isGhost) {
      ctx.strokeStyle = palette.main;
      ctx.lineWidth = 0.08;
      ctx.setLineDash([0.15, 0.1]);
      ctx.stroke();
      ctx.fillStyle = palette.light + '33';
      ctx.fill();
      ctx.restore();
      return;
    }

    ctx.fillStyle = palette.main;
    ctx.fill();

    ctx.beginPath();
    ctx.roundRect(0.06, 0.06, size - 0.12, (size - 0.12) * 0.45, [radius * 0.7, radius * 0.7, 0, 0]);
    ctx.fillStyle = 'rgba(255, 255, 255, 0.35)';
    ctx.fill();

    ctx.beginPath();
    ctx.roundRect(0, 0, size, size, radius);
    ctx.strokeStyle = 'rgba(255, 255, 255, 0.45)';
    ctx.lineWidth = 0.05;
    ctx.stroke();

    ctx.restore();
  }

  function drawGrid(ctx, w, h) {
    ctx.save();
    ctx.strokeStyle = 'rgba(200, 215, 225, 0.25)';
    ctx.lineWidth = 0.02;
    for (let x = 0; x <= w; x++) {
      ctx.beginPath();
      ctx.moveTo(x, 0);
      ctx.lineTo(x, h);
      ctx.stroke();
    }
    for (let y = 0; y <= h; y++) {
      ctx.beginPath();
      ctx.moveTo(0, y);
      ctx.lineTo(w, y);
      ctx.stroke();
    }
    ctx.restore();
  }

  function getGhostPosition() {
    if (!player.matrix) return null;
    const ghost = {
      pos: { x: player.pos.x, y: player.pos.y },
      matrix: player.matrix
    };
    while (!collide(arena, ghost)) {
      ghost.pos.y++;
    }
    ghost.pos.y--;
    return ghost.pos;
  }

  function draw() {
    context.fillStyle = '#fbfcff';
    context.fillRect(0, 0, canvas.width, canvas.height);

    drawGrid(context, 12, 20);

    arena.forEach((row, y) => {
      row.forEach((value, x) => {
        if (value !== 0) drawRoundedBlock(context, x, y, value);
      });
    });

    if (player.matrix) {
      const ghostPos = getGhostPosition();
      if (ghostPos && ghostPos.y !== player.pos.y) {
        player.matrix.forEach((row, y) => {
          row.forEach((value, x) => {
            if (value !== 0) {
              drawRoundedBlock(context, x + ghostPos.x, y + ghostPos.y, value, true);
            }
          });
        });
      }

      player.matrix.forEach((row, y) => {
        row.forEach((value, x) => {
          if (value !== 0) {
            drawRoundedBlock(context, x + player.pos.x, y + player.pos.y, value);
          }
        });
      });
    }
  }

  function drawNext() {
    nextContext.fillStyle = 'rgba(248, 250, 252, 0)';
    nextContext.clearRect(0, 0, nextCanvas.width, nextCanvas.height);
    if (player.next) {
      const piece = createPiece(player.next);
      const offsetX = (5 - piece[0].length) / 2;
      const offsetY = (5 - piece.length) / 2;
      piece.forEach((row, y) => {
        row.forEach((val, x) => {
          if (val !== 0) {
            drawRoundedBlock(nextContext, x + offsetX, y + offsetY, val);
          }
        });
      });
    }
  }

  function merge(arena, player) {
    player.matrix.forEach((row, y) => {
      row.forEach((value, x) => {
        if (value !== 0) {
          arena[y + player.pos.y][x + player.pos.x] = value;
        }
      });
    });
  }

  function playerDrop() {
    if (isPaused) return;
    saveState();
    player.pos.y++;
    if (collide(arena, player)) {
      player.pos.y--;
      merge(arena, player);
      playerReset();
      arenaSweep();
    }
    dropCounter = 0;
  }

  function playerHardDrop() {
    if (isPaused) return;
    saveState();
    while (!collide(arena, player)) {
      player.pos.y++;
    }
    player.pos.y--;
    merge(arena, player);
    playerReset();
    arenaSweep();
    dropCounter = 0;
  }

  function playerMove(dir) {
    if (isPaused) return;
    saveState();
    player.pos.x += dir;
    if (collide(arena, player)) {
      player.pos.x -= dir;
    }
  }

  function playerReset() {
    const pieces = 'ILJOTSZ';
    if (!player.next) {
      player.next = pieces[pieces.length * Math.random() | 0];
    }
    player.matrix = createPiece(player.next);
    player.next = pieces[pieces.length * Math.random() | 0];
    player.pos.y = 0;
    player.pos.x = (arena[0].length / 2 | 0) - (player.matrix[0].length / 2 | 0);

    drawNext();

    if (collide(arena, player)) {
      arena.forEach(row => row.fill(0));
      player.score = 0;
      updateScore();
    }
  }

  function playerRotate(dir) {
    if (isPaused) return;
    saveState();
    const pos = player.pos.x;
    let offset = 1;
    rotate(player.matrix, dir);
    while (collide(arena, player)) {
      player.pos.x += offset;
      offset = -(offset + (offset > 0 ? 1 : -1));
      if (offset > player.matrix[0].length) {
        rotate(player.matrix, -dir);
        player.pos.x = pos;
        return;
      }
    }
  }

  function rotate(matrix, dir) {
    for (let y = 0; y < matrix.length; ++y) {
      for (let x = 0; x < y; ++x) {
        [matrix[x][y], matrix[y][x]] = [matrix[y][x], matrix[x][y]];
      }
    }
    if (dir > 0) matrix.forEach(row => row.reverse());
    else matrix.reverse();
  }

  function update(time = 0) {
    const deltaTime = time - lastTime;
    lastTime = time;

    if (!isPaused && !isFrozen) {
      dropCounter += deltaTime;
      const effectiveInterval = baseSpeed / speedMultiplier;
      if (dropCounter > effectiveInterval) {
        playerDrop();
      }
    }

    draw();
    requestAnimationFrame(update);
  }

  function updateScore() {
    document.getElementById('score').innerText = player.score;
  }

  document.addEventListener('keydown', event => {
    if (event.keyCode === 37) playerMove(-1);
    else if (event.keyCode === 39) playerMove(1);
    else if (event.keyCode === 40) playerDrop();
    else if (event.keyCode === 38) playerRotate(1);
    else if (event.keyCode === 32) {
      event.preventDefault();
      playerHardDrop();
    }
    else if (event.keyCode === 90 || event.keyCode === 122) undo();
    else if (event.keyCode === 80 || event.keyCode === 112) togglePause();
    else if (event.keyCode === 70 || event.keyCode === 102) toggleFreeze();
  });

  const pauseBtn = document.getElementById('btn-pause');
  const freezeBtn = document.getElementById('btn-freeze');
  const undoBtn = document.getElementById('btn-undo');
  const speedSlider = document.getElementById('speed-slider');
  const speedVal = document.getElementById('speed-val');

  function togglePause() {
    isPaused = !isPaused;
    pauseBtn.classList.toggle('active', isPaused);
    pauseBtn.querySelector('.btn-icon').innerText = isPaused ? '▶' : '⏸';
  }

  function toggleFreeze() {
    isFrozen = !isFrozen;
    freezeBtn.classList.toggle('active', isFrozen);
  }

  pauseBtn.addEventListener('click', togglePause);
  freezeBtn.addEventListener('click', toggleFreeze);
  undoBtn.addEventListener('click', undo);

  speedSlider.addEventListener('input', (e) => {
    speedMultiplier = parseFloat(e.target.value);
    speedVal.innerText = speedMultiplier.toFixed(1);
  });

  playerReset();
  updateScore();
  update();
</script>
</body>
</html>
