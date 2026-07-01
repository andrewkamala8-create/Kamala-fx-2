# Kamala-fx-2
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>AI Crypto Analyzer</title>
<script src="https://js.puter.com/v2/"></script>
<style>
* { box-sizing: border-box; margin: 0; padding: 0; }

:root {
  --bg:      #0b0e11;
  --surface: #1e2329;
  --deep:    #161a1e;
  --border:  #2b3139;
  --gold:    #f0b90b;
  --text:    #eaecef;
  --muted:   #848e9c;
  --up:      #0ecb81;
  --down:    #f6465d;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", system-ui, sans-serif;
  background: var(--bg);
  color: var(--text);
  min-height: 100vh;
}

/* ── Layout ── */
.app { display: flex; flex-direction: column; height: 100vh; max-width: 820px; margin: 0 auto; padding: 0 12px; }

/* ── Header ── */
.header { padding: 14px 0 10px; text-align: center; }
.header h1 { color: var(--gold); font-size: 20px; letter-spacing: 0.3px; }
.header p  { color: var(--muted); font-size: 11px; margin-top: 2px; }

/* ── Coin chips ── */
.chips { display: flex; gap: 5px; flex-wrap: wrap; padding-bottom: 8px; }
.chip {
  background: var(--deep); color: var(--muted); border: 1px solid var(--border);
  padding: 5px 11px; border-radius: 20px; font-size: 11.5px; cursor: pointer;
  transition: border-color 0.15s, color 0.15s;
}
.chip:hover, .chip.active { border-color: var(--gold); color: var(--gold); }

/* ── Controls bar ── */
.controls { display: flex; gap: 6px; padding-bottom: 8px; flex-wrap: wrap; }
.controls input {
  flex: 1; min-width: 110px; background: var(--deep); color: var(--text);
  border: 1px solid var(--border); border-radius: 8px; padding: 9px 12px; font-size: 13px;
}
.controls input:focus { outline: none; border-color: var(--gold); }
.controls select {
  flex: 1.8; min-width: 160px; background: var(--deep); color: var(--text);
  border: 1px solid var(--border); border-radius: 8px; padding: 9px 10px; font-size: 12px;
}
.controls select:focus { outline: none; border-color: var(--gold); }
.btn-analyze {
  background: var(--gold); color: #000; font-weight: 700; border: none;
  border-radius: 8px; padding: 9px 20px; font-size: 13px; cursor: pointer;
  white-space: nowrap;
}
.btn-analyze:disabled { opacity: 0.5; cursor: default; }

/* ── Price card ── */
.price-card {
  background: var(--surface); border: 1px solid var(--border); border-radius: 10px;
  padding: 14px 16px; margin-bottom: 8px; display: none;
}
.price-head { display: flex; justify-content: space-between; align-items: baseline; margin-bottom: 4px; }
.price-symbol { color: var(--muted); font-size: 12px; font-weight: 600; letter-spacing: 0.6px; }
.price-val { font-size: 30px; font-weight: 700; margin: 2px 0 10px; }
.stats-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 6px; }
.stat { background: var(--deep); border-radius: 7px; padding: 7px 10px; }
.stat-label { font-size: 10px; color: var(--muted); text-transform: uppercase; letter-spacing: 0.4px; }
.stat-value { font-size: 13px; font-weight: 600; margin-top: 2px; }
.up { color: var(--up); } .down { color: var(--down); }

/* ── Timeframe grid ── */
.tf-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 6px; margin-bottom: 8px; }
.tf-card { background: var(--deep); border: 1px solid var(--border); border-radius: 7px; padding: 8px; text-align: center; }
.tf-label { font-size: 10px; color: var(--muted); text-transform: uppercase; }
.tf-pct   { font-size: 13px; font-weight: 700; margin-top: 2px; }

/* ── Raw data toggle ── */
.raw-toggle {
  width: 100%; background: none; border: 1px solid var(--border); color: var(--muted);
  border-radius: 7px; padding: 6px 12px; font-size: 11.5px; cursor: pointer;
  text-align: left; margin-bottom: 6px; display: none;
}
.raw-toggle:hover { border-color: var(--gold); color: var(--gold); }
.raw-box {
  display: none; background: var(--deep); border: 1px solid var(--border); border-radius: 8px;
  padding: 10px; margin-bottom: 8px; position: relative; max-height: 220px; overflow-y: auto;
}
.raw-box pre {
  font-family: ui-monospace, "SF Mono", Menlo, monospace;
  font-size: 11px; color: #b7bdc6; white-space: pre-wrap; word-break: break-all;
}
.raw-copy-btn {
  position: sticky; top: 0; float: right; background: var(--border); color: var(--text);
  border: none; border-radius: 5px; padding: 3px 9px; font-size: 10.5px; cursor: pointer;
  margin-bottom: 4px;
}
.raw-copy-btn:hover { background: var(--gold); color: #000; }

/* ── Conversation ── */
.chat-wrap {
  flex: 1; display: flex; flex-direction: column; min-height: 0;
  background: var(--surface); border: 1px solid var(--border); border-radius: 10px;
  margin-bottom: 8px; overflow: hidden;
}
.chat-topbar {
  display: flex; align-items: center; justify-content: space-between;
  padding: 9px 14px; border-bottom: 1px solid var(--border); flex-shrink: 0;
}
.chat-topbar-label { font-size: 11.5px; font-weight: 700; color: var(--gold); text-transform: uppercase; letter-spacing: 0.4px; }
.chat-topbar-right { display: flex; gap: 6px; }
.topbar-btn {
  background: var(--deep); color: var(--muted); border: 1px solid var(--border);
  border-radius: 6px; padding: 4px 10px; font-size: 11px; cursor: pointer;
}
.topbar-btn:hover { border-color: var(--gold); color: var(--gold); }

.chat-messages {
  flex: 1; overflow-y: auto; padding: 12px; display: flex;
  flex-direction: column; gap: 10px; scroll-behavior: smooth;
}
.chat-messages::-webkit-scrollbar { width: 4px; }
.chat-messages::-webkit-scrollbar-track { background: transparent; }
.chat-messages::-webkit-scrollbar-thumb { background: var(--border); border-radius: 2px; }

/* Empty state */
.chat-empty {
  flex: 1; display: flex; flex-direction: column; align-items: center;
  justify-content: center; color: var(--muted); text-align: center; gap: 8px; padding: 30px;
}
.chat-empty svg { opacity: 0.25; }
.chat-empty p { font-size: 13px; line-height: 1.5; }

/* Bubbles */
.msg-row { display: flex; gap: 8px; align-items: flex-end; }
.msg-row.user { flex-direction: row-reverse; }

.avatar {
  width: 28px; height: 28px; border-radius: 50%; flex-shrink: 0;
  display: flex; align-items: center; justify-content: center; font-size: 13px;
}
.avatar.ai   { background: #1a2035; border: 1px solid var(--border); }
.avatar.user { background: var(--gold); color: #000; font-weight: 700; font-size: 11px; }

.bubble-wrap { display: flex; flex-direction: column; max-width: 78%; gap: 3px; }
.msg-row.user .bubble-wrap { align-items: flex-end; }

.bubble {
  padding: 10px 13px; border-radius: 12px; font-size: 13.5px;
  line-height: 1.6; word-break: break-word; white-space: pre-wrap; position: relative;
}
.bubble.user { background: var(--gold); color: #000; border-bottom-right-radius: 3px; }
.bubble.ai   { background: var(--deep); color: var(--text); border: 1px solid var(--border); border-bottom-left-radius: 3px; }
.bubble.typing { color: var(--muted); font-style: italic; }

.msg-meta { display: flex; align-items: center; gap: 6px; }
.msg-time { font-size: 10px; color: var(--muted); }
.copy-msg-btn {
  background: none; border: none; color: var(--muted); cursor: pointer; font-size: 11px;
  padding: 1px 5px; border-radius: 4px; opacity: 0;
  transition: opacity 0.15s, color 0.15s;
}
.bubble-wrap:hover .copy-msg-btn { opacity: 1; }
.copy-msg-btn:hover { color: var(--gold); }
.copy-msg-btn.copied { color: var(--up); opacity: 1; }

/* Divider */
.chat-divider { text-align: center; font-size: 10.5px; color: var(--muted); padding: 4px 0; }

/* Input area */
.chat-inputbar {
  border-top: 1px solid var(--border); padding: 10px 12px;
  display: flex; gap: 7px; flex-shrink: 0; align-items: flex-end;
}
.chat-textarea {
  flex: 1; background: var(--deep); color: var(--text); border: 1px solid var(--border);
  border-radius: 8px; padding: 9px 12px; font-size: 13.5px; resize: none;
  font-family: inherit; min-height: 40px; max-height: 110px; line-height: 1.45;
}
.chat-textarea:focus { outline: none; border-color: var(--gold); }
.btn-send {
  background: var(--gold); color: #000; border: none; border-radius: 8px;
  width: 38px; height: 38px; cursor: pointer; flex-shrink: 0;
  display: flex; align-items: center; justify-content: center;
}
.btn-send:disabled { opacity: 0.4; cursor: default; }
.btn-send svg { width: 16px; height: 16px; }

/* ── Memory badge ── */
.memory-badge {
  display: inline-flex; align-items: center; gap: 4px;
  background: var(--deep); border: 1px solid var(--border); border-radius: 20px;
  padding: 2px 8px; font-size: 10.5px; color: var(--muted);
}
.memory-dot { width: 6px; height: 6px; border-radius: 50%; background: var(--up); }

/* ── Spinner ── */
@keyframes spin { to { transform: rotate(360deg); } }
.spin {
  display: inline-block; width: 12px; height: 12px; border: 2px solid var(--border);
  border-top-color: var(--gold); border-radius: 50%; animation: spin 0.7s linear infinite;
}

/* ── Disclaimer ── */
.disclaimer { text-align: center; color: #434b56; font-size: 10.5px; padding: 6px 0 10px; }
</style>
</head>
<body>
<div class="app">

  <!-- Header -->
  <div class="header">
    <h1>📊 AI Crypto Analyzer</h1>
    <p>Live Binance data · multi-timeframe · full conversation memory</p>
  </div>

  <!-- Coin chips -->
  <div class="chips" id="chips">
    <div class="chip" data-sym="BTCUSDT">BTC</div>
    <div class="chip" data-sym="ETHUSDT">ETH</div>
    <div class="chip" data-sym="SOLUSDT">SOL</div>
    <div class="chip" data-sym="BNBUSDT">BNB</div>
    <div class="chip" data-sym="XRPUSDT">XRP</div>
    <div class="chip" data-sym="DOGEUSDT">DOGE</div>
    <div class="chip" data-sym="EURUSDT">EUR</div>
    <div class="chip" data-sym="PAXGUSDT">PAXG</div>
  </div>

  <!-- Controls -->
  <div class="controls">
    <input id="symbolInput" value="BTCUSDT" placeholder="Symbol e.g. BTCUSDT" spellcheck="false">

    <select id="modelSelect">
      <optgroup label="── Claude ──">
        <option value="claude-sonnet-4-6" selected>Claude Sonnet 4.6</option>
        <option value="anthropic/claude-opus-4-8">Claude Opus 4.8</option>
        <option value="anthropic/claude-opus-4-8-fast">Claude Opus 4.8 Fast</option>
        <option value="anthropic/claude-opus-4-7">Claude Opus 4.7</option>
        <option value="anthropic/claude-opus-4-7-fast">Claude Opus 4.7 Fast</option>
        <option value="anthropic/claude-opus-4-6">Claude Opus 4.6</option>
        <option value="anthropic/claude-opus-4-6-fast">Claude Opus 4.6 Fast</option>
        <option value="anthropic/claude-haiku-4-5-20251001">Claude Haiku 4.5</option>
      </optgroup>
      <optgroup label="── OpenAI ──">
        <option value="openai/gpt-5.5">GPT-5.5</option>
        <option value="openai/gpt-5.5-pro">GPT-5.5 Pro</option>
        <option value="openai/gpt-5.4">GPT-5.4</option>
        <option value="openai/gpt-5.4-pro">GPT-5.4 Pro</option>
        <option value="openai/gpt-5.4-mini">GPT-5.4 Mini</option>
        <option value="openai/gpt-5.4-nano">GPT-5.4 Nano</option>
        <option value="openai/gpt-5.3-chat">GPT-5.3 Chat</option>
        <option value="openai/gpt-5.3-codex">GPT-5.3 Codex</option>
        <option value="openai/gpt-5.2">GPT-5.2</option>
        <option value="openai/gpt-5.2-pro">GPT-5.2 Pro</option>
      </optgroup>
      <optgroup label="── Google ──">
        <option value="google/gemini-3.1-pro-preview">Gemini 3.1 Pro</option>
        <option value="google/gemini-3.1-flash-lite">Gemini 3.1 Flash-Lite</option>
        <option value="google/gemini-3-flash-preview">Gemini 3 Flash</option>
      </optgroup>
      <optgroup label="── xAI ──">
        <option value="x-ai/grok-4.3">Grok 4.3</option>
        <option value="x-ai/grok-4.20">Grok 4.20</option>
        <option value="x-ai/grok-4.20-multi-agent">Grok 4.20 Multi-Agent</option>
      </optgroup>
      <optgroup label="── DeepSeek ──">
        <option value="deepseek/deepseek-v4-pro">DeepSeek V4 Pro</option>
        <option value="deepseek/deepseek-v4-flash">DeepSeek V4 Flash</option>
      </optgroup>
    </select>

    <button class="btn-analyze" id="analyzeBtn">Analyze</button>
  </div>

  <!-- Price card -->
  <div class="price-card" id="priceCard"></div>

  <!-- Timeframe grid -->
  <div class="tf-grid" id="tfGrid"></div>

  <!-- Raw data -->
  <button class="raw-toggle" id="rawToggle">▸ Show raw Binance data (JSON)</button>
  <div class="raw-box" id="rawBox">
    <button class="raw-copy-btn" id="rawCopyBtn">Copy</button>
    <pre id="rawContent"></pre>
  </div>

  <!-- ── Conversation ── -->
  <div class="chat-wrap" id="chatWrap">

    <div class="chat-topbar">
      <span class="chat-topbar-label">💬 Conversation</span>
      <div class="chat-topbar-right">
        <span class="memory-badge" id="memBadge">
          <span class="memory-dot"></span> <span id="memCount">0</span> messages in memory
        </span>
        <button class="topbar-btn" id="clearBtn" title="Clear conversation">Clear</button>
      </div>
    </div>

    <div class="chat-messages" id="chatMessages">
      <div class="chat-empty" id="chatEmpty">
        <svg width="40" height="40" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.4">
          <path d="M21 15a2 2 0 0 1-2 2H7l-4 4V5a2 2 0 0 1 2-2h14a2 2 0 0 1 2 2z"/>
        </svg>
        <p>Pick a coin and click <strong>Analyze</strong> to get a full multi-timeframe AI read.<br>Then ask any follow-up — the AI remembers the whole conversation.</p>
      </div>
    </div>

    <div class="chat-inputbar">
      <textarea class="chat-textarea" id="chatInput"
        placeholder="Ask anything about this coin…"
        rows="1"></textarea>
      <button class="btn-send" id="sendBtn" disabled title="Send">
        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.2" stroke-linecap="round" stroke-linejoin="round">
          <line x1="22" y1="2" x2="11" y2="13"/><polygon points="22 2 15 22 11 13 2 9 22 2"/>
        </svg>
      </button>
    </div>

  </div>

  <div class="disclaimer">Market data from Binance public API. AI analysis is informational only — not financial advice.</div>

</div><!-- /app -->

<script>
// ─────────────────────────────────────────────
// STATE
// ─────────────────────────────────────────────
let conversation = [];   // full message history including system prompt
let lastRawData  = null;
let currentSymbol = '';
let isStreaming   = false;

// ─────────────────────────────────────────────
// TIMEFRAMES
// ─────────────────────────────────────────────
const TIMEFRAMES = [
  { interval: '15m', label: '15 Min', limit: 48 },
  { interval: '1h',  label: '1 Hour', limit: 48 },
  { interval: '4h',  label: '4 Hour', limit: 42 },
  { interval: '1d',  label: '1 Day',  limit: 30 }
];

// ─────────────────────────────────────────────
// BINANCE FETCH — races two mirrors with timeout
// ─────────────────────────────────────────────
const HOSTS = ['https://api.binance.com', 'https://data-api.binance.vision'];

function binanceFetch(path) {
  const attempts = HOSTS.map(host => {
    const ctrl = new AbortController();
    const t = setTimeout(() => ctrl.abort(), 6000);
    return fetch(host + path, { signal: ctrl.signal })
      .then(r => r.json())
      .finally(() => clearTimeout(t));
  });
  return Promise.any(attempts);
}

async function fetchAllData(symbol) {
  const [ticker, ...klineResults] = await Promise.all([
    binanceFetch(`/api/v3/ticker/24hr?symbol=${symbol}`),
    ...TIMEFRAMES.map(tf =>
      binanceFetch(`/api/v3/klines?symbol=${symbol}&interval=${tf.interval}&limit=${tf.limit}`)
        .catch(() => null)
    )
  ]);

  const klines = {};
  TIMEFRAMES.forEach((tf, i) => { klines[tf.interval] = klineResults[i]; });
  return { ticker, klines };
}

// ─────────────────────────────────────────────
// FORMAT HELPERS
// ─────────────────────────────────────────────
function fmtPrice(n) {
  const x = parseFloat(n);
  if (isNaN(x)) return n;
  return x.toLocaleString(undefined, { maximumFractionDigits: x < 1 ? 6 : 2 });
}

function fmtTime() {
  return new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' });
}

function tfPct(candles) {
  if (!candles || !candles.length) return null;
  const open  = parseFloat(candles[0][1]);
  const close = parseFloat(candles[candles.length - 1][4]);
  return ((close - open) / open) * 100;
}

// ─────────────────────────────────────────────
// COPY HELPER
// ─────────────────────────────────────────────
function copyText(text, btn, label = 'Copy') {
  navigator.clipboard.writeText(text).then(() => {
    btn.textContent = '✓ Copied';
    btn.classList.add('copied');
    setTimeout(() => {
      btn.textContent = label;
      btn.classList.remove('copied');
    }, 1800);
  }).catch(() => {
    // fallback
    const ta = document.createElement('textarea');
    ta.value = text;
    document.body.appendChild(ta);
    ta.select();
    document.execCommand('copy');
    document.body.removeChild(ta);
    btn.textContent = '✓ Copied';
    setTimeout(() => { btn.textContent = label; }, 1800);
  });
}

// ─────────────────────────────────────────────
// RENDER — price card
// ─────────────────────────────────────────────
function renderPriceCard(ticker, symbol) {
  const pct = parseFloat(ticker.priceChangePercent);
  const dir = pct >= 0 ? 'up' : 'down';
  const arrow = pct >= 0 ? '▲' : '▼';
  const base = symbol.replace(/USDT$/, '');
  document.getElementById('priceCard').style.display = 'block';
  document.getElementById('priceCard').innerHTML = `
    <div class="price-head">
      <span class="price-symbol">${symbol}</span>
      <span class="${dir}">${arrow} ${Math.abs(pct).toFixed(2)}%</span>
    </div>
    <div class="price-val ${dir}">$${fmtPrice(ticker.lastPrice)}</div>
    <div class="stats-grid">
      <div class="stat"><div class="stat-label">24h High</div><div class="stat-value">$${fmtPrice(ticker.highPrice)}</div></div>
      <div class="stat"><div class="stat-label">24h Low</div><div class="stat-value">$${fmtPrice(ticker.lowPrice)}</div></div>
      <div class="stat"><div class="stat-label">Volume</div><div class="stat-value">${parseFloat(ticker.volume).toLocaleString(undefined,{maximumFractionDigits:0})} ${base}</div></div>
      <div class="stat"><div class="stat-label">Wtd Avg</div><div class="stat-value">$${fmtPrice(ticker.weightedAvgPrice)}</div></div>
    </div>`;
}

// ─────────────────────────────────────────────
// RENDER — timeframe grid
// ─────────────────────────────────────────────
function renderTFGrid(klines) {
  document.getElementById('tfGrid').innerHTML = TIMEFRAMES.map(tf => {
    const pct = tfPct(klines[tf.interval]);
    if (pct === null) return `<div class="tf-card"><div class="tf-label">${tf.label}</div><div class="tf-pct" style="color:var(--muted)">N/A</div></div>`;
    const dir = pct >= 0 ? 'up' : 'down';
    const arrow = pct >= 0 ? '▲' : '▼';
    return `<div class="tf-card"><div class="tf-label">${tf.label}</div><div class="tf-pct ${dir}">${arrow} ${Math.abs(pct).toFixed(2)}%</div></div>`;
  }).join('');
}

// ─────────────────────────────────────────────
// RENDER — raw data viewer
// ─────────────────────────────────────────────
function renderRawData(data) {
  const json = JSON.stringify(data, null, 2);
  document.getElementById('rawContent').textContent = json;
  document.getElementById('rawToggle').style.display = 'block';
  document.getElementById('rawToggle').textContent = '▸ Show raw Binance data (JSON)';
  document.getElementById('rawBox').style.display = 'none';
  document.getElementById('rawCopyBtn').onclick = function() {
    copyText(json, this, 'Copy');
  };
}

// ─────────────────────────────────────────────
// CHAT — message management
// ─────────────────────────────────────────────
function hideEmpty() {
  const e = document.getElementById('chatEmpty');
  if (e) e.style.display = 'none';
}

function updateMemBadge() {
  // Only count user/assistant turns (skip system)
  const turns = conversation.filter(m => m.role !== 'system').length;
  document.getElementById('memCount').textContent = turns;
}

function addDivider(text) {
  const div = document.createElement('div');
  div.className = 'chat-divider';
  div.textContent = text;
  document.getElementById('chatMessages').appendChild(div);
}

function addBubble(role, text, streaming = false) {
  hideEmpty();
  const msgs = document.getElementById('chatMessages');

  const row = document.createElement('div');
  row.className = `msg-row ${role === 'user' ? 'user' : 'ai'}`;

  const av = document.createElement('div');
  av.className = `avatar ${role === 'user' ? 'user' : 'ai'}`;
  av.textContent = role === 'user' ? 'You' : '🤖';

  const wrap = document.createElement('div');
  wrap.className = 'bubble-wrap';

  const bubble = document.createElement('div');
  bubble.className = `bubble ${role === 'user' ? 'user' : 'ai'}${streaming ? ' typing' : ''}`;
  bubble.textContent = text;

  const meta = document.createElement('div');
  meta.className = 'msg-meta';

  const timeEl = document.createElement('span');
  timeEl.className = 'msg-time';
  timeEl.textContent = fmtTime();

  const copyBtn = document.createElement('button');
  copyBtn.className = 'copy-msg-btn';
  copyBtn.textContent = '⧉ Copy';
  copyBtn.onclick = function() { copyText(bubble.textContent, this, '⧉ Copy'); };

  if (role === 'user') {
    meta.appendChild(copyBtn);
    meta.appendChild(timeEl);
  } else {
    meta.appendChild(timeEl);
    meta.appendChild(copyBtn);
  }

  wrap.appendChild(bubble);
  wrap.appendChild(meta);

  row.appendChild(av);
  row.appendChild(wrap);
  msgs.appendChild(row);
  msgs.scrollTop = msgs.scrollHeight;

  return bubble;
}

// ─────────────────────────────────────────────
// AI CALL — streamed, model-aware
// ─────────────────────────────────────────────
async function callAI(messages) {
  const model = document.getElementById('modelSelect').value;
  if (typeof puter === 'undefined' || !puter.ai)
    throw new Error('Puter.js not loaded — check your internet and reload.');

  const timeoutMs = 35000;
  const aiTimeout = new Promise((_, rej) =>
    setTimeout(() => rej(new Error('AI took too long. If a sign-in popup appeared, complete it then click Analyze again.')), timeoutMs)
  );

  const stream = await Promise.race([
    puter.ai.chat(messages, { model, stream: true }),
    aiTimeout
  ]);
  return stream;
}

// ─────────────────────────────────────────────
// ANALYZE — full flow
// ─────────────────────────────────────────────
async function analyze() {
  const symbol = document.getElementById('symbolInput').value.trim().toUpperCase();
  if (!symbol || isStreaming) return;

  currentSymbol = symbol;
  isStreaming = true;

  const btn = document.getElementById('analyzeBtn');
  btn.disabled = true;
  btn.textContent = 'Fetching…';

  // Reset UI
  document.getElementById('priceCard').style.display = 'none';
  document.getElementById('tfGrid').innerHTML = '';
  document.getElementById('rawToggle').style.display = 'none';
  document.getElementById('rawBox').style.display = 'none';

  // Clear conversation on new symbol (keep memory only within a session on same symbol)
  if (conversation.length > 0) {
    const msgs = document.getElementById('chatMessages');
    msgs.innerHTML = '';
    const empty = document.createElement('div');
    empty.className = 'chat-empty';
    empty.id = 'chatEmpty';
    empty.innerHTML = `<p style="color:var(--muted);font-size:13px">Loading ${symbol}…</p>`;
    msgs.appendChild(empty);
    conversation = [];
    updateMemBadge();
  }

  try {
    // ── 1. Fetch ALL raw data from Binance public API — unfiltered, unrestricted ──
    btn.textContent = 'Fetching…';
    const rawData = await fetchAllData(symbol);

    if (rawData.ticker.code) {
      throw new Error(rawData.ticker.msg || `Invalid symbol: ${symbol}`);
    }

    lastRawData = rawData;

    // ── 2. Render price + timeframe grid + raw viewer ──
    renderPriceCard(rawData.ticker, symbol);
    renderTFGrid(rawData.klines);
    renderRawData(rawData);

    // ── 3. Build system prompt with FULL unfiltered Binance data ──
    // Kline format: [openTime, open, high, low, close, volume, closeTime,
    //   quoteAssetVolume, numberOfTrades, takerBuyBaseAssetVolume, takerBuyQuoteAssetVolume, ignore]
    const klinesDump = TIMEFRAMES.map(tf => {
      const c = rawData.klines[tf.interval];
      return `## ${tf.interval} candles (${c ? c.length : 0} rows, format: [openTime,open,high,low,close,volume,closeTime,quoteVol,trades,takerBuyBase,takerBuyQuote,ignore]):\n${JSON.stringify(c)}`;
    }).join('\n\n');

    const systemPrompt =
`You are an expert crypto market analyst with access to LIVE, unfiltered, raw data from the Binance public API for ${symbol}.

## RAW 24H TICKER (full Binance payload):
${JSON.stringify(rawData.ticker, null, 2)}

## RAW MULTI-TIMEFRAME KLINE DATA (full Binance klines):
${klinesDump}

Rules:
- Reference exact numbers from the data (prices, volumes, percentages).
- Compare short-term (15m, 1h) with longer-term (4h, 1d) trends and note divergences.
- Keep initial analysis under 180 words. Follow-up answers can be longer if the question warrants it.
- For any market outlook, always end with: "⚠️ Not financial advice."
- You have full memory of this conversation — reference earlier messages when relevant.
- The user can ask anything: technicals, volume analysis, support/resistance, comparisons, etc.`;

    const firstUserMsg = `Give me a full multi-timeframe analysis of ${symbol}: current price momentum, where price sits in today's range, how the 15m/1h trend compares to the 4h/1d trend, notable volume signals, and your overall read on market sentiment.`;

    conversation = [
      { role: 'system',    content: systemPrompt },
      { role: 'user',      content: firstUserMsg }
    ];

    // ── 4. Render the initial user message in chat ──
    const msgs = document.getElementById('chatMessages');
    msgs.innerHTML = '';
    const modelName = document.getElementById('modelSelect').selectedOptions[0].text;
    addDivider(`${symbol} · ${modelName} · ${new Date().toLocaleTimeString()}`);
    addBubble('user', firstUserMsg);

    // ── 5. Stream AI response ──
    btn.textContent = 'Thinking…';
    enableSend(false);

    const aiBubble = addBubble('ai', '…', true);

    const stream = await callAI(conversation);
    aiBubble.textContent = '';
    aiBubble.classList.remove('typing');

    let fullReply = '';
    for await (const part of stream) {
      if (part?.text) {
        fullReply += part.text;
        aiBubble.textContent += part.text;
        msgs.scrollTop = msgs.scrollHeight;
      }
    }

    if (!fullReply) {
      aiBubble.textContent = 'No response — please click Analyze again.';
    } else {
      conversation.push({ role: 'assistant', content: fullReply });
      updateMemBadge();
    }

  } catch (err) {
    hideEmpty();
    addBubble('ai', `⚠️ Error: ${err.message}`);
  }

  btn.disabled = false;
  btn.textContent = 'Analyze';
  enableSend(true);
  isStreaming = false;
}

// ─────────────────────────────────────────────
// SEND CHAT MESSAGE
// ─────────────────────────────────────────────
async function sendChat() {
  const input = document.getElementById('chatInput');
  const text  = input.value.trim();
  if (!text || isStreaming || conversation.length < 2) return;

  isStreaming = true;
  enableSend(false);
  input.value = '';
  input.style.height = 'auto';

  addBubble('user', text);
  conversation.push({ role: 'user', content: text });
  updateMemBadge();

  const aiBubble = addBubble('ai', '…', true);
  const msgs = document.getElementById('chatMessages');

  try {
    const stream = await callAI(conversation);
    aiBubble.textContent = '';
    aiBubble.classList.remove('typing');

    let fullReply = '';
    for await (const part of stream) {
      if (part?.text) {
        fullReply += part.text;
        aiBubble.textContent += part.text;
        msgs.scrollTop = msgs.scrollHeight;
      }
    }

    if (fullReply) {
      conversation.push({ role: 'assistant', content: fullReply });
      updateMemBadge();
    } else {
      aiBubble.textContent = 'No response came back — try again.';
    }
  } catch (err) {
    aiBubble.textContent = `⚠️ ${err.message}`;
  }

  isStreaming = false;
  enableSend(true);
  input.focus();
}

function enableSend(on) {
  document.getElementById('sendBtn').disabled = !on;
}

// ─────────────────────────────────────────────
// CLEAR CONVERSATION
// ─────────────────────────────────────────────
document.getElementById('clearBtn').onclick = () => {
  conversation = [];
  lastRawData  = null;
  currentSymbol = '';
  updateMemBadge();
  enableSend(false);
  const msgs = document.getElementById('chatMessages');
  msgs.innerHTML = `
    <div class="chat-empty" id="chatEmpty">
      <svg width="40" height="40" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.4">
        <path d="M21 15a2 2 0 0 1-2 2H7l-4 4V5a2 2 0 0 1 2-2h14a2 2 0 0 1 2 2z"/>
      </svg>
      <p>Pick a coin and click <strong>Analyze</strong> to get a full multi-timeframe AI read.<br>Then ask any follow-up — the AI remembers the whole conversation.</p>
    </div>`;
  document.getElementById('priceCard').style.display = 'none';
  document.getElementById('tfGrid').innerHTML = '';
  document.getElementById('rawToggle').style.display = 'none';
  document.getElementById('rawBox').style.display = 'none';
};

// ─────────────────────────────────────────────
// RAW TOGGLE
// ─────────────────────────────────────────────
document.getElementById('rawToggle').onclick = function() {
  const box = document.getElementById('rawBox');
  const showing = box.style.display === 'block';
  box.style.display = showing ? 'none' : 'block';
  this.textContent = (showing ? '▸' : '▾') + ' Show raw Binance data (JSON)';
};

// ─────────────────────────────────────────────
// COIN CHIPS
// ─────────────────────────────────────────────
document.getElementById('chips').addEventListener('click', e => {
  const chip = e.target.closest('[data-sym]');
  if (!chip) return;
  document.querySelectorAll('.chip').forEach(c => c.classList.remove('active'));
  chip.classList.add('active');
  document.getElementById('symbolInput').value = chip.dataset.sym;
  analyze();
});

// ─────────────────────────────────────────────
// ANALYZE BUTTON
// ─────────────────────────────────────────────
document.getElementById('analyzeBtn').onclick = analyze;
document.getElementById('symbolInput').addEventListener('keydown', e => {
  if (e.key === 'Enter') analyze();
});

// ─────────────────────────────────────────────
// SEND BUTTON + TEXTAREA auto-resize
// ─────────────────────────────────────────────
document.getElementById('sendBtn').onclick = sendChat;
document.getElementById('chatInput').addEventListener('keydown', e => {
  if (e.key === 'Enter' && !e.shiftKey) { e.preventDefault(); sendChat(); }
});
document.getElementById('chatInput').addEventListener('input', function() {
  this.style.height = 'auto';
  this.style.height = Math.min(this.scrollHeight, 110) + 'px';
  document.getElementById('sendBtn').disabled = !this.value.trim() || isStreaming || conversation.length < 2;
});

// ─────────────────────────────────────────────
// INIT
// ─────────────────────────────────────────────
// Don't auto-analyze on load — first click must be user-initiated
// so Puter's sign-in popup isn't blocked by the browser.
</script>
</body>
</html>
