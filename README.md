# Machigai-sagashi
<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
<title>まちがいさがし</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Nunito:wght@700;800;900&family=M+PLUS+Rounded+1c:wght@700;800&display=swap');

  :root {
    --bg: #FFF9F0;
    --accent1: #FF6B6B;
    --accent3: #FFE66D;
    --accent4: #A8E6CF;
    --dark: #2D2D2D;
    --shadow: 0 4px 20px rgba(0,0,0,0.12);
  }

  * { margin: 0; padding: 0; box-sizing: border-box; -webkit-tap-highlight-color: transparent; }

  body {
    font-family: 'M PLUS Rounded 1c', 'Nunito', sans-serif;
    background: var(--bg);
    min-height: 100vh;
    overflow-x: hidden;
  }

  .screen { display: none; flex-direction: column; align-items: center; min-height: 100vh; padding: 20px; }
  .screen.active { display: flex; }

  /* TITLE */
  #titleScreen {
    background: linear-gradient(160deg, #667eea 0%, #764ba2 100%);
    justify-content: center; gap: 24px;
  }
  .title-deco { font-size: 11px; font-weight: 900; letter-spacing: 4px; color: rgba(255,255,255,0.6); }
  .title-main {
    font-size: 48px; font-weight: 900; color: white;
    text-align: center; line-height: 1.1;
    text-shadow: 0 4px 12px rgba(0,0,0,0.2);
    animation: float 3s ease-in-out infinite;
  }
  @keyframes float { 0%,100%{transform:translateY(0)} 50%{transform:translateY(-10px)} }
  .title-sub { font-size: 16px; color: rgba(255,255,255,0.85); text-align: center; }
  .diff-preview { display: flex; gap: 8px; margin: 10px 0; }
  .diff-dot { width: 14px; height: 14px; border-radius: 50%; animation: pulse 1.5s ease-in-out infinite; }
  .diff-dot:nth-child(1){background:#FF6B6B;animation-delay:0s}
  .diff-dot:nth-child(2){background:#FFE66D;animation-delay:0.2s}
  .diff-dot:nth-child(3){background:#4ECDC4;animation-delay:0.4s}
  .diff-dot:nth-child(4){background:#A8E6CF;animation-delay:0.6s}
  .diff-dot:nth-child(5){background:#FF6B6B;animation-delay:0.8s}
  @keyframes pulse { 0%,100%{transform:scale(1);opacity:0.7} 50%{transform:scale(1.4);opacity:1} }

  .btn-group { display: flex; flex-direction: column; gap: 14px; width: 100%; max-width: 300px; }
  .btn {
    padding: 18px 32px; border: none; border-radius: 50px;
    font-family: 'M PLUS Rounded 1c', sans-serif;
    font-size: 18px; font-weight: 800; cursor: pointer; transition: all 0.15s ease;
  }
  .btn:active { transform: scale(0.96); }
  .btn-easy { background: var(--accent4); color: #1a6b50; box-shadow: 0 6px 0 #5fb893; }
  .btn-easy:active { box-shadow: 0 2px 0 #5fb893; transform: translateY(4px); }
  .btn-hard { background: var(--accent1); color: white; box-shadow: 0 6px 0 #c84b4b; }
  .btn-hard:active { box-shadow: 0 2px 0 #c84b4b; transform: translateY(4px); }
  .btn-label { font-size: 12px; font-weight: 700; opacity: 0.7; display: block; margin-top: 2px; }

  /* GAME */
  #gameScreen { background: var(--bg); padding: 12px; gap: 0; }
  .game-header {
    width: 100%; display: flex; justify-content: space-between;
    align-items: center; padding: 10px 4px; margin-bottom: 8px;
  }
  .timer-wrap, .miss-wrap {
    display: flex; flex-direction: column; align-items: center;
    background: white; border-radius: 16px; padding: 8px 14px;
    box-shadow: var(--shadow); min-width: 80px;
  }
  .timer-label, .miss-label, .progress-label {
    font-size: 10px; font-weight: 800; color: #999; letter-spacing: 1px;
  }
  .timer-val {
    font-family: 'Nunito', sans-serif; font-size: 28px; font-weight: 900;
    color: var(--dark); line-height: 1; transition: color 0.3s;
  }
  .timer-val.urgent { color: var(--accent1); animation: shake 0.5s ease-in-out infinite; }
  @keyframes shake { 0%,100%{transform:translateX(0)} 25%{transform:translateX(-3px)} 75%{transform:translateX(3px)} }
  .progress-wrap { display: flex; flex-direction: column; align-items: center; gap: 6px; }
  .stars-row { display: flex; gap: 5px; }
  .star-item {
    width: 24px; height: 24px; border-radius: 50%;
    background: #eee; display: flex; align-items: center; justify-content: center; font-size: 13px;
  }
  .star-item.found { background: var(--accent3); box-shadow: 0 0 10px rgba(255,230,109,0.8); animation: popIn 0.4s cubic-bezier(0.175,0.885,0.32,1.275); }
  @keyframes popIn { 0%{transform:scale(0)} 100%{transform:scale(1)} }
  .hearts-row { display: flex; gap: 3px; }
  .heart { font-size: 18px; transition: all 0.3s; }
  .heart.lost { filter: grayscale(1) opacity(0.3); }

  .game-images { width: 100%; display: flex; flex-direction: column; gap: 8px; }
  .image-label { font-size: 11px; font-weight: 800; color: #aaa; letter-spacing: 2px; text-align: center; margin-bottom: 3px; }

  /* SVGラッパー：overflowを隠す */
  .img-wrap {
    position: relative; width: 100%;
    border-radius: 16px; overflow: hidden;
    box-shadow: var(--shadow); user-select: none;
    touch-action: none;
  }
  /* ゲームSVG */
  .game-svg { width: 100%; height: auto; display: block; }
  /* オーバーレイSVG：同じviewBoxで重ねる */
  .overlay-svg {
    position: absolute; top: 0; left: 0; width: 100%; height: 100%;
    pointer-events: none;
  }

  .hint-bar {
    width: 100%; text-align: center;
    font-size: 13px; font-weight: 800; color: #bbb; padding: 8px 0 2px;
  }

  /* RESULT */
  #resultScreen { justify-content: center; gap: 20px; }
  .result-bg-clear { background: linear-gradient(160deg, #a8edea 0%, #fed6e3 100%); }
  .result-bg-over  { background: linear-gradient(160deg, #f093fb 0%, #f5576c 100%); }
  .result-emoji { font-size: 80px; animation: float 3s ease-in-out infinite; }
  .result-title { font-size: 40px; font-weight: 900; color: white; text-shadow: 0 3px 10px rgba(0,0,0,0.15); text-align: center; }
  .result-subtitle { font-size: 15px; color: rgba(255,255,255,0.9); text-align: center; }
  .result-stats {
    background: rgba(255,255,255,0.3); backdrop-filter: blur(10px);
    border-radius: 24px; padding: 20px 32px; display: flex; gap: 32px; text-align: center;
  }
  .stat-val { font-family:'Nunito',sans-serif; font-size: 36px; font-weight: 900; color: white; }
  .stat-label { font-size: 12px; font-weight: 800; color: rgba(255,255,255,0.8); letter-spacing: 1px; }
  .btn-retry { background: white; color: #764ba2; box-shadow: 0 6px 0 rgba(0,0,0,0.15); }
  .btn-retry:active { box-shadow: 0 2px 0 rgba(0,0,0,0.15); transform: translateY(4px); }
  .btn-home { background: rgba(255,255,255,0.25); color: white; border: 3px solid rgba(255,255,255,0.6); }

  .screen-flash { position: fixed; inset: 0; pointer-events: none; z-index: 100; opacity: 0; }
  @keyframes flashAnim { 0%{opacity:0} 20%{opacity:1} 100%{opacity:0} }
</style>
</head>
<body>

<div class="screen-flash" id="flashEl"></div>

<!-- TITLE -->
<div class="screen active" id="titleScreen">
  <div class="title-deco">✦ FIND THE DIFFERENCE ✦</div>
  <div class="title-main">まちがい<br>さがし</div>
  <div class="diff-preview">
    <div class="diff-dot"></div><div class="diff-dot"></div>
    <div class="diff-dot"></div><div class="diff-dot"></div>
    <div class="diff-dot"></div>
  </div>
  <div class="title-sub">２枚の絵の違いをタップしよう！</div>
  <div class="btn-group" style="margin-top:16px;">
    <button class="btn btn-easy" onclick="startGame('easy')">
      🌟 かんたん<span class="btn-label">3分・まちがい3つ</span>
    </button>
    <button class="btn btn-hard" onclick="startGame('hard')">
      🔥 むずかしい<span class="btn-label">5分・まちがい5つ</span>
    </button>
  </div>
</div>

<!-- GAME -->
<div class="screen" id="gameScreen">
  <div class="game-header">
    <div class="timer-wrap">
      <div class="timer-label">TIME</div>
      <div class="timer-val" id="timerVal">3:00</div>
    </div>
    <div class="progress-wrap">
      <div class="progress-label">FOUND</div>
      <div class="stars-row" id="starsRow"></div>
    </div>
    <div class="miss-wrap">
      <div class="miss-label">MISS</div>
      <div class="hearts-row" id="heartsRow"></div>
    </div>
  </div>

  <div class="game-images">
    <div>
      <div class="image-label">▲ もとの絵</div>
      <div class="img-wrap" id="wrap1"></div>
    </div>
    <div>
      <div class="image-label">▼ まちがいをさがしてね</div>
      <div class="img-wrap" id="wrap2"></div>
    </div>
  </div>
  <div class="hint-bar" id="hintBar">タップして違いをさがそう！</div>
</div>

<!-- RESULT -->
<div class="screen" id="resultScreen">
  <div class="result-emoji" id="resultEmoji">🎉</div>
  <div class="result-title" id="resultTitle">クリア！</div>
  <div class="result-subtitle" id="resultSubtitle"></div>
  <div class="result-stats">
    <div><div class="stat-val" id="statFound">0</div><div class="stat-label">みつけた</div></div>
    <div><div class="stat-val" id="statTime">0:00</div><div class="stat-label">タイム</div></div>
    <div><div class="stat-val" id="statMiss">0</div><div class="stat-label">おてつき</div></div>
  </div>
  <div class="btn-group" style="max-width:280px;">
    <button class="btn btn-retry" onclick="retryGame()">🔄 もう一回</button>
    <button class="btn btn-home" onclick="goHome()">🏠 トップへ</button>
  </div>
</div>

<script>
// ============================================================
// viewBox サイズ（全SVG共通）
// ============================================================
const VB_W = 360, VB_H = 220;

// ============================================================
// パズル定義
// cx, cy, r は SVG viewBox 座標（px）で指定
// ============================================================
const PUZZLES = {
  easy: {
    totalTime: 180,
    differences: [
      { id: 0, cx: 86,  cy: 152, r: 24 }, // 花
      { id: 1, cx: 287, cy: 79,  r: 24 }, // 鳥
      { id: 2, cx: 180, cy: 188, r: 28 }, // 道
    ],
  },
  hard: {
    totalTime: 300,
    differences: [
      { id: 0, cx: 86,  cy: 152, r: 24 }, // 花
      { id: 1, cx: 287, cy: 79,  r: 24 }, // 鳥
      { id: 2, cx: 180, cy: 188, r: 28 }, // 道
      { id: 3, cx: 50,  cy: 54,  r: 28 }, // 雲
      { id: 4, cx: 180, cy: 152, r: 22 }, // ベンチ
    ],
  },
};

// ============================================================
// シーン SVG 生成
// ============================================================
function makeSceneSVG(modified, difficulty) {
  const isHard = difficulty === 'hard';

  const flowerColor = modified ? '#FFE030' : '#FF7EB3';          // diff 0
  const birdColor   = modified ? '#4A90E2' : '#E83030';          // diff 1
  const pathColor   = modified ? '#F0A020' : '#DEC68A';          // diff 2
  const benchColor  = (modified && isHard) ? '#9B30D0' : '#8B6914'; // diff 4

  // diff 3: 雲の形
  const cloudLeft = (modified && isHard)
    ? `<ellipse cx="42" cy="58" rx="20" ry="13" fill="white"/>
       <ellipse cx="62" cy="50" rx="18" ry="12" fill="white"/>`
    : `<ellipse cx="32" cy="60" rx="16" ry="11" fill="white"/>
       <ellipse cx="50" cy="52" rx="22" ry="14" fill="white"/>
       <ellipse cx="68" cy="59" rx="17" ry="11" fill="white"/>`;

  return `<svg class="game-svg" viewBox="0 0 ${VB_W} ${VB_H}" xmlns="http://www.w3.org/2000/svg">
  <!-- 空 -->
  <rect width="360" height="220" fill="#B8E8FF"/>

  <!-- 太陽 -->
  <circle cx="310" cy="35" r="26" fill="#FFD93D"/>
  <circle cx="310" cy="35" r="19" fill="#FFE976"/>

  <!-- 雲（左）diff3 -->
  ${cloudLeft}

  <!-- 雲（右）変化なし -->
  <ellipse cx="210" cy="32" rx="28" ry="15" fill="white"/>
  <ellipse cx="230" cy="24" rx="20" ry="13" fill="white"/>
  <ellipse cx="244" cy="32" rx="18" ry="12" fill="white"/>

  <!-- 地面 -->
  <rect y="148" width="360" height="72" fill="#7EC850"/>
  <ellipse cx="80"  cy="153" rx="108" ry="44" fill="#5DB33B"/>
  <ellipse cx="285" cy="158" rx="112" ry="46" fill="#5DB33B"/>

  <!-- 道  diff2: pathColor  中心 (180, 188) -->
  <path d="M125,220 Q160,165 180,150 Q202,165 235,220" fill="${pathColor}"/>

  <!-- ベンチ  diff4: benchColor  中心 (180,152) -->
  <rect x="155" y="150" width="50" height="6"  rx="3" fill="${benchColor}"/>
  <rect x="162" y="156" width="5"  height="11" rx="2" fill="${benchColor}"/>
  <rect x="193" y="156" width="5"  height="11" rx="2" fill="${benchColor}"/>
  <rect x="155" y="142" width="50" height="5"  rx="2" fill="${benchColor}" opacity="0.8"/>

  <!-- 木（左） -->
  <rect x="40" y="105" width="12" height="48" fill="#8B6914" rx="3"/>
  <ellipse cx="46" cy="95" rx="28" ry="34" fill="#3A8C2A"/>
  <ellipse cx="46" cy="87" rx="20" ry="24" fill="#4DAF3A"/>

  <!-- 木（右） -->
  <rect x="294" y="108" width="12" height="44" fill="#8B6914" rx="3"/>
  <ellipse cx="300" cy="98" rx="26" ry="32" fill="#3A8C2A"/>
  <ellipse cx="300" cy="90" rx="18" ry="22" fill="#4DAF3A"/>

  <!-- 鳥  diff1: birdColor  中心 (287, 79) -->
  <ellipse cx="279" cy="82" rx="10" ry="7"  fill="${birdColor}"/>
  <circle  cx="288" cy="76" r="6"            fill="${birdColor}"/>
  <circle  cx="292" cy="75" r="2"            fill="#111"/>
  <polygon points="295,75 301,76 295,78"     fill="#FFD700"/>
  <path d="M271,81 Q263,73 269,82" stroke="${birdColor}" stroke-width="5" fill="none" stroke-linecap="round"/>
  <path d="M271,83 Q263,91 269,83" stroke="${birdColor}" stroke-width="4" fill="none" stroke-linecap="round" opacity="0.8"/>

  <!-- 花  diff0: flowerColor  中心 (86, 152) -->
  <rect x="84" y="154" width="4" height="15" fill="#4CAF50" rx="2"/>
  <circle cx="86" cy="142" r="8" fill="${flowerColor}"/>
  <circle cx="86" cy="162" r="8" fill="${flowerColor}"/>
  <circle cx="76" cy="152" r="8" fill="${flowerColor}"/>
  <circle cx="96" cy="152" r="8" fill="${flowerColor}"/>
  <circle cx="79" cy="145" r="7" fill="${flowerColor}"/>
  <circle cx="93" cy="145" r="7" fill="${flowerColor}"/>
  <circle cx="79" cy="159" r="7" fill="${flowerColor}"/>
  <circle cx="93" cy="159" r="7" fill="${flowerColor}"/>
  <circle cx="86" cy="152" r="8" fill="white"/>
  <circle cx="86" cy="152" r="5" fill="#FFD700"/>

  <!-- 小花（左） -->
  <rect x="68" y="159" width="3" height="11" fill="#4CAF50" rx="1.5"/>
  <circle cx="70" cy="151" r="5" fill="${flowerColor}"/>
  <circle cx="70" cy="160" r="5" fill="${flowerColor}"/>
  <circle cx="65" cy="156" r="5" fill="${flowerColor}"/>
  <circle cx="75" cy="156" r="5" fill="${flowerColor}"/>
  <circle cx="70" cy="156" r="4" fill="white"/>
  <circle cx="70" cy="156" r="2.5" fill="#FFD700"/>

  <!-- 蝶 -->
  <g transform="translate(138,112) rotate(15)">
    <ellipse cx="-8" cy="-4" rx="9" ry="6" fill="#FF9FF3" opacity="0.85"/>
    <ellipse cx="8"  cy="-4" rx="9" ry="6" fill="#FF9FF3" opacity="0.85"/>
    <ellipse cx="-6" cy="4"  rx="6" ry="4" fill="#FF9FF3" opacity="0.7"/>
    <ellipse cx="6"  cy="4"  rx="6" ry="4" fill="#FF9FF3" opacity="0.7"/>
    <rect x="-1" y="-8" width="2" height="16" rx="1" fill="#555"/>
  </g>

  <!-- 池 -->
  <ellipse cx="255" cy="170" rx="38" ry="17" fill="#66B3E8" opacity="0.7"/>
  <ellipse cx="250" cy="167" rx="28" ry="10" fill="#89C9F4" opacity="0.5"/>

  <!-- アヒル -->
  <ellipse cx="254" cy="168" rx="12" ry="8" fill="white"/>
  <circle  cx="264" cy="163" r="7"          fill="white"/>
  <circle  cx="268" cy="162" r="1.8"        fill="#333"/>
  <polygon points="271,163 277,163 271,165" fill="#FFA500"/>

  <!-- 草 -->
  <g fill="#4CAF50" opacity="0.7">
    <path d="M20,150 Q22,138 24,150"/>
    <path d="M28,148 Q30,136 32,148"/>
    <path d="M325,153 Q327,141 329,153"/>
    <path d="M333,151 Q335,139 337,151"/>
    <path d="M163,148 Q165,138 167,148"/>
    <path d="M172,146 Q174,137 176,146"/>
  </g>
</svg>`;
}

// ============================================================
// ゲーム状態
// ============================================================
let state = {
  difficulty: 'easy',
  timeLeft: 180,
  timerInterval: null,
  found: [],
  misses: 0,
  maxMisses: 3,
  puzzle: null,
  startTime: null,
  ended: false,
};

// ============================================================
// タップ処理
// SVG viewBox 座標系に変換して当たり判定
// ============================================================
function handleTap(e, wrapId) {
  if (state.ended) return;
  e.preventDefault();

  const wrap = document.getElementById(wrapId);
  // ゲームSVG要素を取得（overlayではなく game-svg）
  const svgEl = wrap.querySelector('.game-svg');
  const rect = svgEl.getBoundingClientRect();

  const clientX = e.touches ? e.touches[0].clientX : e.clientX;
  const clientY = e.touches ? e.touches[0].clientY : e.clientY;

  // SVG要素上の相対位置
  const relX = clientX - rect.left;
  const relY = clientY - rect.top;

  // viewBox 座標に変換（アスペクト比を正しく反映）
  const svgX = (relX / rect.width)  * VB_W;
  const svgY = (relY / rect.height) * VB_H;

  // 当たり判定
  let hit = false;
  for (const diff of state.puzzle.differences) {
    if (state.found.includes(diff.id)) continue;
    const dx = svgX - diff.cx;
    const dy = svgY - diff.cy;
    if (Math.sqrt(dx * dx + dy * dy) <= diff.r) {
      hit = true;
      foundDiff(diff.id);
      break;
    }
  }
  if (!hit) {
    miss(svgX, svgY);
  }
}

// ============================================================
// 正解
// ============================================================
function foundDiff(id) {
  state.found.push(id);
  const diff = state.puzzle.differences.find(d => d.id === id);

  // 両方の overlay に赤丸
  addSVGMarker('overlay1', diff.cx, diff.cy, 'found');
  addSVGMarker('overlay2', diff.cx, diff.cy, 'found');

  // スター
  const star = document.getElementById('star_' + id);
  if (star) star.classList.add('found');

  doFlash('rgba(255,230,109,0.55)');

  const remaining = state.puzzle.differences.length - state.found.length;
  document.getElementById('hintBar').textContent =
    remaining > 0 ? `あと ${remaining} つ！` : 'ぜんぶみつけた！🎉';

  if (state.found.length >= state.puzzle.differences.length) {
    setTimeout(() => endGame(true), 700);
  }
}

// ============================================================
// ミス
// ============================================================
function miss(svgX, svgY) {
  state.misses++;

  // バツを両方に一時表示
  const m1 = addSVGMarker('overlay1', svgX, svgY, 'miss');
  const m2 = addSVGMarker('overlay2', svgX, svgY, 'miss');
  setTimeout(() => { m1.remove(); m2.remove(); }, 900);

  const heartIdx = state.maxMisses - state.misses;
  const heart = document.getElementById('heart_' + heartIdx);
  if (heart) heart.classList.add('lost');

  doFlash('rgba(255,107,107,0.45)');

  document.getElementById('hintBar').textContent = '💦 おてつき！';
  setTimeout(() => {
    if (!state.ended) {
      const rem = state.puzzle.differences.length - state.found.length;
      document.getElementById('hintBar').textContent = `あと ${rem} つ！`;
    }
  }, 1200);

  if (state.misses >= state.maxMisses) {
    setTimeout(() => endGame(false, 'miss'), 600);
  }
}

// ============================================================
// SVG マーカー追加（viewBox 座標で描く → 表示位置が正確）
// ============================================================
function addSVGMarker(overlayId, cx, cy, type) {
  const ns = 'http://www.w3.org/2000/svg';
  const overlay = document.getElementById(overlayId);
  const g = document.createElementNS(ns, 'g');

  const circle = document.createElementNS(ns, 'circle');
  circle.setAttribute('cx', cx);
  circle.setAttribute('cy', cy);
  circle.setAttribute('r', '22');

  if (type === 'found') {
    circle.setAttribute('fill', 'rgba(255,60,60,0.18)');
    circle.setAttribute('stroke', '#FF3030');
    circle.setAttribute('stroke-width', '3.5');
    g.appendChild(circle);
  } else {
    circle.setAttribute('fill', 'rgba(60,120,220,0.18)');
    circle.setAttribute('stroke', '#3A78DC');
    circle.setAttribute('stroke-width', '3');
    g.appendChild(circle);

    const mk = (x1,y1,x2,y2) => {
      const l = document.createElementNS(ns, 'line');
      l.setAttribute('x1', x1); l.setAttribute('y1', y1);
      l.setAttribute('x2', x2); l.setAttribute('y2', y2);
      l.setAttribute('stroke', '#3A78DC');
      l.setAttribute('stroke-width', '4');
      l.setAttribute('stroke-linecap', 'round');
      return l;
    };
    g.appendChild(mk(cx-13, cy-13, cx+13, cy+13));
    g.appendChild(mk(cx+13, cy-13, cx-13, cy+13));
  }

  overlay.appendChild(g);
  return g;
}

// ============================================================
// フラッシュ
// ============================================================
function doFlash(color) {
  const el = document.getElementById('flashEl');
  el.style.background = color;
  el.style.animation = 'none';
  el.offsetHeight; // reflow
  el.style.animation = 'flashAnim 0.5s ease forwards';
}

// ============================================================
// ゲーム開始
// ============================================================
function startGame(difficulty) {
  state.difficulty = difficulty;
  state.puzzle     = PUZZLES[difficulty];
  state.timeLeft   = state.puzzle.totalTime;
  state.found      = [];
  state.misses     = 0;
  state.ended      = false;
  state.startTime  = Date.now();

  // 画像とオーバーレイを挿入
  const wrap1 = document.getElementById('wrap1');
  const wrap2 = document.getElementById('wrap2');

  wrap1.innerHTML = makeSceneSVG(false, difficulty)
    + `<svg class="overlay-svg" id="overlay1" viewBox="0 0 ${VB_W} ${VB_H}" xmlns="http://www.w3.org/2000/svg"></svg>`;
  wrap2.innerHTML = makeSceneSVG(true, difficulty)
    + `<svg class="overlay-svg" id="overlay2" viewBox="0 0 ${VB_W} ${VB_H}" xmlns="http://www.w3.org/2000/svg"></svg>`;

  // タップイベント（addEventListener で登録）
  function bindTap(wrapId) {
    const w = document.getElementById(wrapId);
    // 古いリスナーを消すためにcloneで置き換え
    const fresh = w.cloneNode(true);
    w.parentNode.replaceChild(fresh, w);
    const fw = document.getElementById(wrapId);
    fw.addEventListener('touchstart', e => handleTap(e, wrapId), { passive: false });
    fw.addEventListener('click',      e => handleTap(e, wrapId));
  }
  bindTap('wrap1');
  bindTap('wrap2');

  buildStars();
  buildHearts();
  updateTimer();
  document.getElementById('hintBar').textContent = 'タップして違いをさがそう！';
  showScreen('gameScreen');

  if (state.timerInterval) clearInterval(state.timerInterval);
  state.timerInterval = setInterval(tick, 1000);
}

function tick() {
  if (state.ended) return;
  state.timeLeft--;
  updateTimer();
  if (state.timeLeft <= 0) endGame(false, 'timeout');
}

function updateTimer() {
  const m = Math.floor(state.timeLeft / 60);
  const s = state.timeLeft % 60;
  const el = document.getElementById('timerVal');
  el.textContent = `${m}:${s.toString().padStart(2, '0')}`;
  el.classList.toggle('urgent', state.timeLeft <= 30);
}

function buildStars() {
  const row = document.getElementById('starsRow');
  row.innerHTML = '';
  state.puzzle.differences.forEach((_, i) => {
    const s = document.createElement('div');
    s.className = 'star-item'; s.id = 'star_' + i; s.textContent = '⭐';
    row.appendChild(s);
  });
}

function buildHearts() {
  const row = document.getElementById('heartsRow');
  row.innerHTML = '';
  for (let i = 0; i < state.maxMisses; i++) {
    const h = document.createElement('div');
    h.className = 'heart'; h.id = 'heart_' + i; h.textContent = '❤️';
    row.appendChild(h);
  }
}

function endGame(cleared, reason) {
  if (state.ended) return;
  state.ended = true;
  clearInterval(state.timerInterval);

  const elapsed = Math.round((Date.now() - state.startTime) / 1000);
  const m = Math.floor(elapsed / 60);
  const s = elapsed % 60;

  const rs = document.getElementById('resultScreen');
  rs.className = 'screen ' + (cleared ? 'result-bg-clear' : 'result-bg-over');
  document.getElementById('resultEmoji').textContent    = cleared ? '🎉' : (reason === 'timeout' ? '⏰' : '💔');
  document.getElementById('resultTitle').textContent    = cleared ? 'クリア！' : (reason === 'timeout' ? 'タイムアップ！' : 'ゲームオーバー！');
  document.getElementById('resultSubtitle').textContent = cleared
    ? 'すごい！全部見つけた！'
    : (reason === 'timeout' ? '時間切れ！また挑戦してね！' : 'おてつき3回！また頑張ってね！');
  document.getElementById('statFound').textContent = state.found.length;
  document.getElementById('statTime').textContent  = `${m}:${s.toString().padStart(2, '0')}`;
  document.getElementById('statMiss').textContent  = state.misses;

  setTimeout(() => showScreen('resultScreen'), 800);
}

function retryGame() { startGame(state.difficulty); }
function goHome()    { showScreen('titleScreen'); }

function showScreen(id) {
  document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
  document.getElementById(id).classList.add('active');
}
</script>
