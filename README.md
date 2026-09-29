# <!DOCTYPE html>
<html lang="zh-TW">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>療癒系俄羅斯方塊</title>
  <style>
    :root {
      --bg-color: #f7f9fb;
      --card-bg: #ffffff;
      --text-color: #5a6578;
      --accent-color: #8da9c4;
      --accent-hover: #0b2545;
      --border-color: #e2e8f0;
    }

    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
      font-family: 'PingFang TC', 'Microsoft JhengHei', sans-serif;
    }

    body {
      background-color: var(--bg-color);
      color: var(--text-color);
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
      padding: 20px;
    }

    .container {
      display: flex;
      gap: 20px;
      background: var(--card-bg);
      padding: 24px;
      border-radius: 16px;
      box-shadow: 0 10px 30px rgba(0,0,0,0.05);
      max-width: 900px;
      width: 100%;
      flex-wrap: wrap;
      justify-content: center;
    }

    .game-area {
      position: relative;
    }

    #tetris {
      border: 2px solid var(--border-color);
      border-radius: 8px;
      background-color: #fdfdfd;
    }

    .sidebar {
      display: flex;
      flex-direction: column;
      gap: 16px;
      width: 260px;
    }

    .panel {
      background: #f8fafc;
      padding: 16px;
      border-radius: 12px;
      border: 1px solid var(--border-color);
    }

    h2 {
      font-size: 1.1rem;
      margin-bottom: 8px;
      color: #334155;
    }

    .stat-val {
      font-size: 1.5rem;
      font-weight: bold;
      color: var(--accent-color);
    }

    .controls {
      display: flex;
      flex-direction: column;
      gap: 8px;
    }

    button {
      background-color: #e2e8f0;
      color: #334155;
      border: none;
      padding: 10px 14px;
      border-radius: 8px;
      font-size: 0.95rem;
      cursor: pointer;
      transition: all 0.2s ease;
      display: flex;
      align-items: center;
      justify-content: space-between;
    }

    button:hover {
      background-color: var(--accent-color);
      color: white;
    }

    button.active {
      background-color: #0b2545;
      color: white;
    }

    .slider-group {
      display: flex;
      flex-direction: column;
      gap: 6px;
      margin-top: 6px;
    }

    input[type="range"] {
      width: 100%;
      accent-color: var(--accent-color);
    }

    .key-hints {
      font-size: 0.85rem;
      line-height: 1.6;
      color: #64748b;
    }

    .key-hints kbd {
      background: #e2e8f0;
      padding: 2px 6px;
      border-radius: 4px;
      font-family: monospace;
    }
  </style>
</head>
<body>

<div class="container">
  <div class="game-area">
    <canvas id="tetris" width="240" height="400"></canvas>
  </div>

  <div class="sidebar">
    <div class="panel">
      <h2>分數</h2>
      <div id="score" class="stat-val">0</div>
    </div>

    <div class="panel">
      <h2>下一塊</h2>
      <canvas id="next" width="100" height="100"></canvas>
    </div>

    <div class="panel controls">
      <h2>療癒輔助功能</h2>
      <button id="btn-pause">暫停 (P) <span>⏸</span></button>
      <button id="btn-undo">回上一步 (Z) <span>↩</span></button>
      <button id="btn-freeze">停止向下掉 (F) <span>🛑</span></button>

      <div class="slider-group">
        <label for="speed-slider">下落速度: <span id="speed-val">1.0</span>x</label>
        <input type="range" id="speed-slider" min="0.1" max="2" step="0.1" value="1.0">
      </div>
    </div>

    <div class="panel key-hints">
      <strong>操作說明：</strong><br>
      <kbd>←</kbd> <kbd>→</kbd> 移動 | <kbd>↑</kbd> 旋轉<br>
      <kbd>↓</kbd> 加速下降 | <kbd>Space</kbd> 直接落底<br>
      <kbd>Z</kbd> 復原 | <kbd>F</kbd> 懸浮固定 | <kbd>P</kbd> 暫停
    </div>
  </div>
</div>

<script>
  const canvas = document.getElementById('tetris');
  const context = canvas.getContext('2d');
  const nextCanvas = document.getElementById('next');
  const nextContext = nextCanvas.getContext('2d');

  context.scale(20, 20);
  nextContext.scale(20, 20);

  // 療癒色調莫蘭迪配色
  const COLORS = [
    null,
    '#90a4ae', // I - 灰藍
    '#b0bec5', // J - 淺藍灰
    '#d7ccc8', // L - 暖灰
    '#fff59d', // O - 柔黃
    '#a5d6a7', // S - 薄荷綠
    '#ce93d8', // T - 淡紫
    '#ef9a9a', // Z - 柔粉
  ];

  function createMatrix(w, h) {
    const matrix = [];
    while (h--) {
      matrix.push(new Array(w).fill(0));
    }
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

  // 遊戲狀態控制
  let isPaused = false;
  let isFrozen = false;
  let baseSpeed = 1000; // 毫秒
  let speedMultiplier = 1.0;
  let dropCounter = 0;
  let lastTime = 0;

  // Undo 歷史紀錄 Stack
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
    
    // 復原盤面與玩家狀態
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
        if (arena[y][x] === 0) {
          continue outer;
        }
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

  function draw() {
    context.fillStyle = '#fdfdfd';
    context.fillRect(0, 0, canvas.width, canvas.height);

    drawMatrix(arena, {x: 0, y: 0}, context);
    if (player.matrix) {
      drawMatrix(player.matrix, player.pos, context);
    }
  }

  function drawNext() {
    nextContext.fillStyle = '#f8fafc';
    nextContext.fillRect(0, 0, nextCanvas.width, nextCanvas.height);
    if (player.next) {
      const nextPiece = createPiece(player.next);
      // 置中繪製
      const offsetX = (5 - nextPiece[0].length) / 2;
      const offsetY = (5 - nextPiece.length) / 2;
      drawMatrix(nextPiece, {x: offsetX, y: offsetY}, nextContext);
    }
  }

  function drawMatrix(matrix, offset, ctx) {
    matrix.forEach((row, y) => {
      row.forEach((value, x) => {
        if (value !== 0) {
          ctx.fillStyle = COLORS[value];
          ctx.fillRect(x + offset.x, y + offset.y, 1, 1);
          // 繪製柔和邊框
          ctx.lineWidth = 0.05;
          ctx.strokeStyle = '#ffffff';
          ctx.strokeRect(x + offset.x, y + offset.y, 1, 1);
        }
      });
    });
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

    // 遊戲結束檢查
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
        [
          matrix[x][y],
          matrix[y][x],
        ] = [
          matrix[y][x],
          matrix[x][y],
        ];
      }
    }
    if (dir > 0) {
      matrix.forEach(row => row.reverse());
    } else {
      matrix.reverse();
    }
  }

  function update(time = 0) {
    const deltaTime = time - lastTime;
    lastTime = time;

    // 若非暫停且未開啟「停止掉落」，則計時下落
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

  // 事件綁定：鍵盤操作
  document.addEventListener('keydown', event => {
    if (event.keyCode === 37) { // Left
      playerMove(-1);
    } else if (event.keyCode === 39) { // Right
      playerMove(1);
    } else if (event.keyCode === 40) { // Down
      playerDrop();
    } else if (event.keyCode === 38) { // Up (Rotate)
      playerRotate(1);
    } else if (event.keyCode === 32) { // Space (Hard drop)
      playerHardDrop();
    } else if (event.keyCode === 90 || event.keyCode === 122) { // Z (Undo)
      undo();
    } else if (event.keyCode === 80 || event.keyCode === 112) { // P (Pause)
      togglePause();
    } else if (event.keyCode === 70 || event.keyCode === 102) { // F (Freeze/Stop drop)
      toggleFreeze();
    }
  });

  // UI 按鈕控制
  const pauseBtn = document.getElementById('btn-pause');
  const freezeBtn = document.getElementById('btn-freeze');
  const undoBtn = document.getElementById('btn-undo');
  const speedSlider = document.getElementById('speed-slider');
  const speedVal = document.getElementById('speed-val');

  function togglePause() {
    isPaused = !isPaused;
    pauseBtn.classList.toggle('active', isPaused);
    pauseBtn.querySelector('span').innerText = isPaused ? '▶' : '⏸';
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

  // 初始化遊戲
  playerReset();
  updateScore();
  update();
</script>
</body>
</html>
