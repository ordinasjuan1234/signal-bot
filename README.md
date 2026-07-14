<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Signal Bot Pro</title>
<style>
*{box-sizing:border-box;margin:0;padding:0}
body{background:#0a0a0f;color:#e0e0e0;font-family:'Courier New',monospace;min-height:100vh}
.header{background:#0d0d18;border-bottom:1px solid #1e1e2e;padding:10px 14px;display:flex;align-items:center;justify-content:space-between}
.dot{width:8px;height:8px;border-radius:50%;display:inline-block;margin-right:8px}
.dot-green{background:#00ff88;box-shadow:0 0 8px #00ff88}
.dot-orange{background:#ff8800;box-shadow:0 0 8px #ff8800}
.tabs{display:flex;border-bottom:1px solid #1e1e2e;overflow-x:auto}
.tab{flex:1;min-width:60px;background:none;border:none;border-bottom:2px solid transparent;color:#444;padding:9px 0;font-size:10px;cursor:pointer;font-family:inherit;letter-spacing:1px;white-space:nowrap}
.tab.active{border-bottom-color:#6666ff;color:#8888ff}
.content{padding:12px 12px 80px}
.card{background:#0d0d18;border:1px solid #1e1e2e;border-radius:10px;padding:12px;margin-bottom:10px}
.card-label{font-size:9px;color:#333;letter-spacing:2px;margin-bottom:8px}
select,input{background:#111;border:1px solid #2a2a3e;color:#e0e0e0;border-radius:8px;padding:7px 10px;font-size:11px;font-family:inherit;outline:none}
.btn{border-radius:8px;padding:8px 14px;font-size:11px;cursor:pointer;font-family:inherit;border:1px solid;font-weight:600}
.btn-green{background:#0d2d1a;border-color:#00ff88;color:#00ff88}
.btn-red{background:#2d0d0d;border-color:#ff3355;color:#ff3355}
.btn-blue{background:#0d0d2d;border-color:#6666ff;color:#8888ff}
.btn-gray{background:#111;border-color:#333;color:#555}
.btn-orange{background:#1f1000;border-color:#ff8800;color:#ff8800}
.grid2{display:grid;grid-template-columns:1fr 1fr;gap:8px}
.grid3{display:grid;grid-template-columns:1fr 1fr 1fr;gap:6px}
.mini-card{background:#111;border-radius:8px;padding:8px;text-align:center}
.mini-label{font-size:9px;color:#444;margin-bottom:3px}
.mini-value{font-size:14px;font-weight:700}
.signal-box{border-radius:10px;padding:10px 16px;text-align:center;border-width:2px;border-style:solid;flex:1}
.progress-bar{height:5px;background:#222;border-radius:3px;overflow:hidden;margin-top:8px}
.progress-fill{height:100%;border-radius:3px;transition:width 0.6s}
.indicator-row{display:flex;justify-content:space-between;padding:5px 0;border-bottom:1px solid #0d0d0d}
.trade-item{border-radius:10px;padding:10px;margin-bottom:7px;border:1px solid}
.stat-card{background:#0d0d18;border:1px solid #1e1e2e;border-radius:10px;padding:10px}
.alert-banner{border-radius:10px;padding:12px;margin-bottom:10px;border:1px solid;animation:pulse 2s infinite}
@keyframes pulse{0%,100%{opacity:1}50%{opacity:0.7}}
.tf-btn{background:#111;border:1px solid #2a2a3e;color:#555;border-radius:6px;padding:5px 8px;font-size:10px;cursor:pointer;font-family:inherit}
.tf-btn.active{background:#1e1e3e;border-color:#6666ff;color:#8888ff}
.mtf-row{display:flex;gap:4px;flex-wrap:wrap;margin-bottom:8px}
.mtf-badge{font-size:9px;padding:3px 6px;border-radius:4px;font-weight:700}
.risk-input{width:100%;margin-bottom:8px}
.disclaimer{margin-top:10px;padding:8px;background:#0d0d0d;border-radius:8px;font-size:9px;color:#1e1e1e;line-height:1.6}
.auto-status{display:flex;align-items:center;gap:8px;margin-bottom:10px}
.auto-dot{width:10px;height:10px;border-radius:50%}
</style>
</head>
<body>

<div class="header">
  <div style="display:flex;align-items:center">
    <span class="dot dot-green" id="modeDot"></span>
    <span style="font-weight:800;font-size:12px;letter-spacing:3px;color:#fff">SIGNAL BOT PRO</span>
    <span id="modeLabel" style="font-size:9px;color:#00ff8866;border:1px solid #00ff8822;border-radius:4px;padding:1px 6px;margin-left:8px">DEMO</span>
  </div>
  <div style="text-align:right">
    <div style="font-size:9px;color:#444" id="modeIndicator">DEMO · CAPITAL</div>
    <div style="font-size:12px;font-weight:700" id="capitalDisplay">$1000.00</div>
    <div style="font-size:10px;font-weight:700;display:none" id="realBalanceDisplay"></div>
  </div>
</div>

<div class="tabs" id="mainTabs">
  <button class="tab active" onclick="switchTab('signal')">SEÑAL</button>
  <button class="tab" onclick="switchTab('auto')">AUTO</button>
  <button class="tab" onclick="switchTab('risk')">RIESGO</button>
  <button class="tab" onclick="switchTab('trades')">TRADES</button>
  <button class="tab" onclick="switchTab('stats')">STATS</button>
</div>

<div class="content">

  <!-- CONTROLS -->
  <div style="display:flex;gap:5px;margin-bottom:10px;flex-wrap:wrap">
    <select id="pairSelect" onchange="onPairChange()" style="flex:1;min-width:110px">
      <option value="BTCUSDT">BTC/USDT</option>
      <option value="ETHUSDT">ETH/USDT</option>
      <option value="BNBUSDT">BNB/USDT</option>
      <option value="SOLUSDT">SOL/USDT</option>
      <option value="XRPUSDT">XRP/USDT</option>
      <option value="DOGEUSDT">DOGE/USDT</option>
      <option value="ADAUSDT">ADA/USDT</option>
      <option value="AVAXUSDT">AVAX/USDT</option>
      <option value="MATICUSDT">MATIC/USDT</option>
      <option value="LINKUSDT">LINK/USDT</option>
    </select>
    <input id="customPair" placeholder="Otro..." style="flex:1;min-width:80px" onkeydown="if(event.key==='Enter')addCustomPair()"/>
    <button onclick="addCustomPair()" class="btn btn-blue" style="padding:7px 10px">+</button>
    <button onclick="loadAllTimeframes()" id="refreshBtn" class="btn btn-green" style="padding:7px 12px">↻</button>
  </div>

  <!-- TIMEFRAME SELECTOR -->
  <div class="mtf-row">
    <span style="font-size:10px;color:#444;margin-right:4px;align-self:center">TF:</span>
    <button class="tf-btn" onclick="setTF('5m',this)">5m</button>
    <button class="tf-btn active" onclick="setTF('15m',this)">15m</button>
    <button class="tf-btn" onclick="setTF('1h',this)">1h</button>
    <button class="tf-btn" onclick="setTF('4h',this)">4h</button>
    <button class="tf-btn" onclick="setTF('1d',this)">1D</button>
    <button class="tf-btn" onclick="setTF('1w',this)">1S</button>
  </div>

  <div id="errorBox" style="display:none;background:#1a0808;border:1px solid #ff3355;border-radius:10px;padding:10px;color:#ff3355;font-size:11px;margin-bottom:10px"></div>

  <!-- SIGNAL TAB -->
  <div id="tab-signal">
    <div id="alertBanner" style="display:none" class="alert-banner"></div>
    <div id="loadingMsg" style="text-align:center;padding:30px;color:#444;font-size:12px">Analizando mercado...</div>
    <div id="signalContent" style="display:none">
      <!-- Multi-TF Summary -->
      <div class="card" id="mtfCard">
        <div class="card-label">ANÁLISIS MULTI-TIMEFRAME</div>
        <div id="mtfSummary" style="display:flex;gap:4px;flex-wrap:wrap"></div>
        <div id="mtfConsensus" style="margin-top:8px;font-size:11px;color:#666"></div>
      </div>

      <!-- Main Signal -->
      <div class="card" id="signalCard">
        <div style="display:flex;align-items:center;gap:8px;margin-bottom:10px">
          <div class="signal-box" id="signalBox">
            <div id="signalIcon" style="font-size:20px"></div>
            <div id="signalText" style="font-size:16px;font-weight:800;letter-spacing:2px"></div>
          </div>
          <div style="flex:1">
            <div class="mini-card" style="margin-bottom:5px">
              <div class="mini-label">DIRECCIÓN</div>
              <div class="mini-value" id="directionText"></div>
            </div>
            <div class="mini-card">
              <div class="mini-label">CONFIANZA</div>
              <div class="mini-value" id="confidenceText"></div>
            </div>
          </div>
        </div>
        <div class="grid2" style="margin-bottom:8px">
          <div class="mini-card"><div class="mini-label">ALCISTAS</div><div class="mini-value" style="color:#00ff88" id="bullScore">0</div></div>
          <div class="mini-card"><div class="mini-label">BAJISTAS</div><div class="mini-value" style="color:#ff3355" id="bearScore">0</div></div>
        </div>
        <div class="progress-bar"><div class="progress-fill" id="progressFill"></div></div>
        <div style="display:flex;justify-content:space-between;margin-top:6px;font-size:10px;color:#444">
          <span id="pairLabel"></span>
          <span id="priceDisplay"></span>
        </div>
      </div>

      <!-- Levels -->
      <div class="card">
        <div class="card-label">NIVELES DE OPERACIÓN</div>
        <div class="grid3" style="margin-bottom:8px">
          <div style="background:#111;border-radius:8px;padding:7px;text-align:center">
            <div style="font-size:9px;color:#555">ENTRADA</div>
            <div style="font-size:11px;font-weight:700;color:#fff" id="entryPrice"></div>
          </div>
          <div style="background:#0d2d1a;border:1px solid #00ff8833;border-radius:8px;padding:7px;text-align:center">
            <div style="font-size:9px;color:#00ff8877">TAKE PROFIT</div>
            <div style="font-size:11px;font-weight:700;color:#00ff88" id="tpPrice"></div>
          </div>
          <div style="background:#2d0d0d;border:1px solid #ff335533;border-radius:8px;padding:7px;text-align:center">
            <div style="font-size:9px;color:#ff335577">STOP LOSS</div>
            <div style="font-size:11px;font-weight:700;color:#ff3355" id="slPrice"></div>
          </div>
        </div>
        <div style="display:flex;justify-content:space-between;padding:4px 0;border-top:1px solid #111">
          <span style="font-size:10px;color:#444">Ratio R/R</span>
          <span style="font-size:11px;font-weight:700" id="rrText"></span>
        </div>
        <div style="display:flex;justify-content:space-between;padding:4px 0;border-top:1px solid #111">
          <span style="font-size:10px;color:#444">Tamaño posición sugerido</span>
          <span style="font-size:11px;font-weight:700;color:#8888ff" id="posSizeText"></span>
        </div>
      </div>

      <!-- Indicators -->
      <div class="card">
        <div class="card-label">INDICADORES</div>
        <div id="indicatorsList"></div>
      </div>

      <!-- Action -->
      <div id="actionArea"></div>
    </div>
  </div>

  <!-- AUTO TAB -->
  <div id="tab-auto" style="display:none">
    <div class="card">
      <div class="card-label">MODO AUTOMÁTICO</div>
      <div class="auto-status">
        <div class="auto-dot" id="autoDot" style="background:#333"></div>
        <span style="font-size:12px;font-weight:700" id="autoStatusText">INACTIVO</span>
      </div>
      <div class="grid2" style="margin-bottom:10px">
        <div>
          <div style="font-size:10px;color:#555;margin-bottom:4px">Límite ganancia diaria</div>
          <input type="number" id="maxDailyGain" value="5" min="1" max="50" style="width:100%"> 
          <div style="font-size:9px;color:#333;margin-top:2px">% del capital</div>
        </div>
        <div>
          <div style="font-size:10px;color:#555;margin-bottom:4px">Límite pérdida diaria</div>
          <input type="number" id="maxDailyLoss" value="3" min="1" max="20" style="width:100%">
          <div style="font-size:9px;color:#333;margin-top:2px">% del capital</div>
        </div>
      </div>
      <div style="margin-bottom:10px">
        <div style="font-size:10px;color:#555;margin-bottom:4px">Confianza mínima para operar</div>
        <input type="range" id="minConfidence" min="50" max="90" value="70" style="width:100%;accent-color:#6666ff" oninput="document.getElementById('confValue').textContent=this.value+'%'">
        <div style="display:flex;justify-content:space-between;font-size:9px;color:#444">
          <span>50%</span><span id="confValue" style="color:#8888ff">70%</span><span>90%</span>
        </div>
      </div>
      <div style="margin-bottom:10px">
        <div style="font-size:10px;color:#555;margin-bottom:4px">Timeframes a monitorear</div>
        <div style="display:flex;gap:4px;flex-wrap:wrap" id="autoTFSelector">
          <button class="tf-btn active" data-tf="15m" onclick="toggleAutoTF(this)">15m</button>
          <button class="tf-btn active" data-tf="1h" onclick="toggleAutoTF(this)">1h</button>
          <button class="tf-btn" data-tf="4h" onclick="toggleAutoTF(this)">4h</button>
          <button class="tf-btn" data-tf="1d" onclick="toggleAutoTF(this)">1D</button>
        </div>
      </div>
      <div style="margin-bottom:10px">
        <div style="font-size:10px;color:#555;margin-bottom:4px">Requerir confirmación multi-TF</div>
        <label style="display:flex;align-items:center;gap:8px;cursor:pointer">
          <input type="checkbox" id="requireMTF" checked style="accent-color:#6666ff">
          <span style="font-size:11px;color:#888">Solo operar cuando 2+ timeframes coinciden</span>
        </label>
      </div>
      <div style="display:flex;gap:6px">
        <button onclick="toggleAuto()" id="autoBtn" class="btn btn-blue" style="flex:2;padding:12px 0;font-size:12px">▶ ACTIVAR AUTO</button>
        <button onclick="stopAuto()" class="btn btn-gray" style="flex:1;padding:12px 0">■ STOP</button>
      </div>
      <div id="autoLog" style="margin-top:10px;font-size:10px;color:#444;max-height:120px;overflow-y:auto"></div>
    </div>

    <div class="card" style="border-color:#ff880033">
      <div class="card-label" style="color:#ff8800">⚡ MODO REAL — BINANCE</div>
      <div style="margin-bottom:8px">
        <div style="font-size:10px;color:#555;margin-bottom:4px">API Key</div>
        <input type="password" id="apiKeyInput" placeholder="Tu API Key de Binance" style="width:100%;margin-bottom:6px" value="">
        <div style="font-size:10px;color:#555;margin-bottom:4px">Secret Key</div>
        <input type="password" id="apiSecretInput" placeholder="Tu Secret Key de Binance" style="width:100%;margin-bottom:8px" value="">
        <div style="display:flex;gap:6px">
          <button onclick="saveApiKeys()" class="btn btn-orange" style="flex:2;padding:10px 0">💾 Guardar claves</button>
          <button onclick="testConnection()" class="btn btn-blue" style="flex:1;padding:10px 0">🔌 Probar</button>
        </div>
        <div id="connectionStatus" style="margin-top:8px;font-size:10px;color:#444;text-align:center"></div>
      </div>
      <div style="margin-bottom:8px">
        <div style="font-size:10px;color:#555;margin-bottom:4px">% del saldo a usar por operación</div>
        <div style="display:flex;gap:4px;flex-wrap:wrap" id="pctSelector">
          <button class="tf-btn active" data-pct="5" onclick="selectPct(this)">5%</button>
          <button class="tf-btn" data-pct="10" onclick="selectPct(this)">10%</button>
          <button class="tf-btn" data-pct="25" onclick="selectPct(this)">25%</button>
          <button class="tf-btn" data-pct="50" onclick="selectPct(this)">50%</button>
        </div>
      </div>
      <label style="display:flex;align-items:center;gap:8px;cursor:pointer;margin-bottom:8px">
        <input type="checkbox" id="realModeToggle" onchange="toggleRealMode()" style="accent-color:#ff8800">
        <span style="font-size:11px;color:#ff8800;font-weight:700">ACTIVAR MODO REAL (órdenes con dinero real)</span>
      </label>
      <div style="font-size:9px;color:#333;line-height:1.6">⚠ Las órdenes en modo real se ejecutan inmediatamente en Binance con dinero real. Usá gestión de riesgo.</div>
    </div>

    <div class="card">
      <div class="card-label">ESTADÍSTICAS HOY</div>
      <div class="grid2">
        <div class="mini-card"><div class="mini-label">P&L HOY</div><div class="mini-value" id="dailyPnl" style="color:#00ff88">+$0.00</div></div>
        <div class="mini-card"><div class="mini-label">OPERACIONES HOY</div><div class="mini-value" id="dailyTrades" style="color:#8888ff">0</div></div>
        <div class="mini-card"><div class="mini-label">GANANCIA LÍMITE</div><div class="mini-value" id="gainLimit" style="color:#00ff88">$0.00</div></div>
        <div class="mini-card"><div class="mini-label">PÉRDIDA LÍMITE</div><div class="mini-value" id="lossLimit" style="color:#ff3355">$0.00</div></div>
      </div>
    </div>
  </div>

  <!-- RISK TAB -->
  <div id="tab-risk" style="display:none">
    <div class="card">
      <div class="card-label">CALCULADORA DE RIESGO</div>
      <div style="margin-bottom:10px">
        <div style="font-size:10px;color:#555;margin-bottom:4px">Capital total (USD)</div>
        <input type="number" id="riskCapital" value="1000" style="width:100%" oninput="calcRisk()">
      </div>
      <div style="margin-bottom:10px">
        <div style="font-size:10px;color:#555;margin-bottom:4px">% de riesgo por operación</div>
        <input type="range" id="riskPct" min="0.5" max="5" step="0.5" value="2" style="width:100%;accent-color:#ff3355" oninput="document.getElementById('riskPctVal').textContent=this.value+'%';calcRisk()">
        <div style="display:flex;justify-content:space-between;font-size:9px;color:#444">
          <span>0.5%</span><span id="riskPctVal" style="color:#ff3355">2%</span><span>5%</span>
        </div>
      </div>
      <div style="background:#111;border-radius:8px;padding:12px;margin-bottom:10px">
        <div style="display:flex;justify-content:space-between;margin-bottom:6px">
          <span style="font-size:10px;color:#555">Máximo a perder por operación</span>
          <span style="font-size:12px;font-weight:700;color:#ff3355" id="maxLoss">$20.00</span>
        </div>
        <div style="display:flex;justify-content:space-between;margin-bottom:6px">
          <span style="font-size:10px;color:#555">Capital a usar por operación</span>
          <span style="font-size:12px;font-weight:700;color:#8888ff" id="capitalPerTrade">$200.00</span>
        </div>
        <div style="display:flex;justify-content:space-between">
          <span style="font-size:10px;color:#555">Operaciones hasta liquidar</span>
          <span style="font-size:12px;font-weight:700;color:#fff" id="tradesToBust">50</span>
        </div>
      </div>
      <div style="margin-bottom:10px">
        <div style="font-size:10px;color:#555;margin-bottom:4px">Precio de entrada</div>
        <input type="number" id="riskEntry" placeholder="Ej: 63000" style="width:100%" oninput="calcPositionSize()">
      </div>
      <div style="margin-bottom:10px">
        <div style="font-size:10px;color:#555;margin-bottom:4px">Stop Loss</div>
        <input type="number" id="riskSL" placeholder="Ej: 62000" style="width:100%" oninput="calcPositionSize()">
      </div>
      <div style="background:#0d0d2d;border:1px solid #3333aa;border-radius:8px;padding:12px">
        <div style="font-size:9px;color:#6666ff;letter-spacing:2px;margin-bottom:8px">RESULTADO</div>
        <div style="display:flex;justify-content:space-between;margin-bottom:4px">
          <span style="font-size:10px;color:#555">Cantidad a comprar</span>
          <span style="font-size:13px;font-weight:700;color:#8888ff" id="qtyResult">—</span>
        </div>
        <div style="display:flex;justify-content:space-between">
          <span style="font-size:10px;color:#555">Valor de la posición</span>
          <span style="font-size:13px;font-weight:700;color:#fff" id="posValue">—</span>
        </div>
      </div>
    </div>

    <div class="card">
      <div class="card-label">REGLAS DE GESTIÓN</div>
      <div style="font-size:11px;color:#555;line-height:2">
        <div>✓ Nunca arriesgues más del <span style="color:#ff3355" id="riskRule">2%</span> por operación</div>
        <div>✓ Límite pérdida diaria: <span style="color:#ff3355" id="dailyLossRule">$30.00</span></div>
        <div>✓ Límite ganancia diaria: <span style="color:#00ff88" id="dailyGainRule">$50.00</span></div>
        <div>✓ Máximo 3 operaciones perdedoras seguidas → parar</div>
        <div>✓ Nunca operar en mercado NEUTRO</div>
        <div>✓ Esperar confirmación multi-TF para señales fuertes</div>
      </div>
    </div>
  </div>

  <!-- TRADES TAB -->
  <div id="tab-trades" style="display:none">
    <div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:10px">
      <div style="font-size:10px;color:#333;letter-spacing:2px">HISTORIAL</div>
      <button onclick="exportTrades()" class="btn btn-blue" style="padding:5px 10px;font-size:9px">↓ EXPORTAR</button>
    </div>
    <div id="tradesList"></div>
  </div>

  <!-- STATS TAB -->
  <div id="tab-stats" style="display:none">
    <div class="grid2" id="statsGrid" style="margin-bottom:10px"></div>
    <div class="card" style="margin-bottom:10px">
      <div class="card-label">RENDIMIENTO POR TIMEFRAME</div>
      <div id="tfStats"></div>
    </div>
    <div class="card" style="margin-bottom:10px">
      <div class="card-label">RACHA ACTUAL</div>
      <div id="streakInfo" style="font-size:13px;color:#888;text-align:center;padding:8px"></div>
    </div>
    <button onclick="resetAll()" class="btn btn-gray" style="width:100%;padding:9px 0;font-size:10px">↺ Reiniciar todo</button>
  </div>

  <div class="disclaimer">MODO DEMO — Sin dinero real. Señales orientativas, no constituyen consejo financiero.</div>
</div>

<script>
// ── Storage ──────────────────────────────────────────────
function _get(k){try{const m=document.cookie.match(new RegExp('(?:^|; )'+k+'=([^;]*)'));return m?decodeURIComponent(m[1]):null}catch(e){return null}}
function _set(k,v){try{const d=new Date();d.setFullYear(d.getFullYear()+1);document.cookie=k+'='+encodeURIComponent(String(v))+';expires='+d.toUTCString()+';path=/;SameSite=Lax'}catch(e){}}
function _remove(k){try{document.cookie=k+'=;expires=Thu, 01 Jan 1970 00:00:00 UTC;path=/;'}catch(e){}}

// ── Backend ──────────────────────────────────────────────
const BACKEND_URL = "https://trading-backend-x7dw.onrender.com";
let apiKey = _get('apiKey') || '';
let apiSecret = _get('apiSecret') || '';
let realBalance = null;
let isRealMode = false;

async function backendPost(endpoint, body) {
  const res = await fetch(BACKEND_URL + endpoint, {
    method: 'POST',
    headers: {'Content-Type': 'application/json'},
    body: JSON.stringify(body)
  });
  return res.json();
}

async function fetchRealBalance() {
  if (!apiKey || !apiSecret) return;
  try {
    const data = await backendPost('/balance', {apiKey, apiSecret});
    if (data.usdt !== undefined) {
      realBalance = data.usdt;
      document.getElementById('realBalanceDisplay').textContent = '$' + realBalance.toFixed(2) + ' USDT';
      document.getElementById('realBalanceDisplay').style.color = '#ff8800';
    }
  } catch(e) { console.log('Balance error:', e); }
}

async function placeRealOrder(side, quantity) {
  if (!apiKey || !apiSecret) return {error: 'Sin claves API'};
  return await backendPost('/order', {apiKey, apiSecret, symbol: pair, side, quantity: quantity.toString()});
}

// ── State ─────────────────────────────────────────────────
let pair='BTCUSDT';
let currentTF='15m';
let analysis=null;
let capital=parseFloat(_get('capital'))||1000;
let openTrade=null;
try{const s=_get('openTrade');if(s&&s!=='null')openTrade=JSON.parse(s)}catch(e){}
let trades=[];
try{const s=_get('trades');if(s&&s!=='null')trades=JSON.parse(s)}catch(e){}
const INITIAL_CAPITAL=1000;
let mtfData={};
let autoMode=false;
let autoTimer=null;
let autoTFs=['15m','1h'];
let dailyPnl=parseFloat(_get('dailyPnl'))||0;
let dailyTrades=parseInt(_get('dailyTrades'))||0;
let consecutiveLosses=0;

// ── Format ────────────────────────────────────────────────
function fp(p){
  if(!p&&p!==0)return '-';
  if(p>10000)return p.toLocaleString('es-AR',{maximumFractionDigits:0});
  if(p>1000)return p.toLocaleString('es-AR',{maximumFractionDigits:2});
  if(p>1)return p.toFixed(4);
  return p.toFixed(6);
}

// ── Technical Analysis ────────────────────────────────────
function calcEMA(d,p){if(d.length<p)return null;const k=2/(p+1);let e=d.slice(0,p).reduce((a,b)=>a+b,0)/p;for(let i=p;i<d.length;i++)e=d[i]*k+e*(1-k);return e}
function calcSMA(d,p){if(d.length<p)return null;return d.slice(-p).reduce((a,b)=>a+b,0)/p}
function calcRSI(c,p=14){if(c.length<p+1)return null;let g=0,l=0;for(let i=c.length-p;i<c.length;i++){const d=c[i]-c[i-1];if(d>=0)g+=d;else l-=d}const ag=g/p,al=l/p;if(al===0)return 100;return 100-100/(1+ag/al)}
function calcBB(c,p=20){if(c.length<p)return null;const s=c.slice(-p);const m=s.reduce((a,b)=>a+b,0)/p;const std=Math.sqrt(s.reduce((a,b)=>a+Math.pow(b-m,2),0)/p);return{upper:m+2*std,middle:m,lower:m-2*std}}
function calcMACD(c){if(c.length<26)return null;const e12=calcEMA(c,12),e26=calcEMA(c,26);if(!e12||!e26)return null;return{macdLine:e12-e26}}
function calcATR(h,l,c,p=14){if(h.length<p+1)return null;let atr=0;for(let i=h.length-p;i<h.length;i++){const tr=Math.max(h[i]-l[i],Math.abs(h[i]-c[i-1]),Math.abs(l[i]-c[i-1]));atr+=tr}return atr/p}

function analyze(closes,highs,lows){
  if(!closes||closes.length<30)return null;
  const price=closes[closes.length-1];
  const rsi=calcRSI(closes);
  const macd=calcMACD(closes);
  const bb=calcBB(closes);
  const sma20=calcSMA(closes,20);
  const ema50=calcEMA(closes,Math.min(50,closes.length));
  const sma200=closes.length>=200?calcSMA(closes,200):null;
  const atr=calcATR(highs,lows,closes)||price*0.02;
  let bull=0,bear=0;
  const reasons=[];
  if(rsi!==null){
    if(rsi<35){bull+=2;reasons.push({l:'RSI sobrevendido',v:rsi.toFixed(1),b:true})}
    else if(rsi>65){bear+=2;reasons.push({l:'RSI sobrecomprado',v:rsi.toFixed(1),b:false})}
    else reasons.push({l:'RSI neutro',v:rsi.toFixed(1),b:null})
  }
  if(macd){
    if(macd.macdLine>0){bull+=1;reasons.push({l:'MACD positivo',v:macd.macdLine.toFixed(4),b:true})}
    else{bear+=1;reasons.push({l:'MACD negativo',v:macd.macdLine.toFixed(4),b:false})}
  }
  if(bb){
    if(price<bb.lower){bull+=2;reasons.push({l:'Bajo banda BB',v:price.toFixed(2),b:true})}
    else if(price>bb.upper){bear+=2;reasons.push({l:'Sobre banda BB',v:price.toFixed(2),b:false})}
    else reasons.push({l:'Dentro de Bollinger',v:`${bb.lower.toFixed(2)}-${bb.upper.toFixed(2)}`,b:null})
  }
  if(sma20){
    if(price>sma20){bull+=1;reasons.push({l:'Precio > SMA20',v:sma20.toFixed(2),b:true})}
    else{bear+=1;reasons.push({l:'Precio < SMA20',v:sma20.toFixed(2),b:false})}
  }
  if(ema50){
    if(price>ema50){bull+=1;reasons.push({l:'Precio > EMA50',v:ema50.toFixed(2),b:true})}
    else{bear+=1;reasons.push({l:'Precio < EMA50',v:ema50.toFixed(2),b:false})}
  }
  if(sma200){
    if(price>sma200){bull+=1;reasons.push({l:'Tendencia SMA200 alcista',v:sma200.toFixed(2),b:true})}
    else{bear+=1;reasons.push({l:'Tendencia SMA200 bajista',v:sma200.toFixed(2),b:false})}
  }
  const total=bull+bear;
  const conf=total>0?Math.round((Math.max(bull,bear)/total)*100):50;
  const diff=bull-bear;
  let signal,direction;
  if(diff>=2){signal='COMPRAR';direction='LARGO'}
  else if(diff<=-2){signal='VENDER';direction='SHORT'}
  else{signal='NEUTRO';direction='ESPERAR'}
  const rH=Math.max(...highs.slice(-14));
  const rL=Math.min(...lows.slice(-14));
  const range=rH-rL;
  let entry=price,tp,sl;
  if(signal==='COMPRAR'){tp=price+range*0.618;sl=price-range*0.382}
  else if(signal==='VENDER'){tp=price-range*0.618;sl=price+range*0.382}
  else{tp=price+range*0.3;sl=price-range*0.3}
  const rr=Math.abs(tp-entry)/Math.abs(sl-entry);
  // Position size based on 2% risk
  const riskAmt=capital*0.02;
  const riskPerUnit=Math.abs(entry-sl);
  const posSize=riskPerUnit>0?(riskAmt/riskPerUnit).toFixed(4):0;
  return{signal,direction,confidence:conf,price,entry,tp,sl,rr,reasons,bullScore:bull,bearScore:bear,rsi,atr,posSize}
}

// ── Fetch Data ────────────────────────────────────────────
async function fetchKlines(p,tf,limit=200){
  const res=await fetch(`https://api.binance.com/api/v3/klines?symbol=${p}&interval=${tf}&limit=${limit}`);
  if(!res.ok)throw new Error('Par no encontrado');
  const data=await res.json();
  return{closes:data.map(k=>parseFloat(k[4])),highs:data.map(k=>parseFloat(k[2])),lows:data.map(k=>parseFloat(k[3]))}
}

async function loadData(){
  document.getElementById('loadingMsg').style.display='block';
  document.getElementById('signalContent').style.display='none';
  document.getElementById('errorBox').style.display='none';
  document.getElementById('refreshBtn').textContent='...';
  try{
    const{closes,highs,lows}=await fetchKlines(pair,currentTF);
    analysis=analyze(closes,highs,lows);
    renderSignal();
    checkAutoTrade();
  }catch(e){
    document.getElementById('errorBox').textContent='⚠ '+e.message;
    document.getElementById('errorBox').style.display='block';
    document.getElementById('loadingMsg').style.display='none';
  }
  document.getElementById('refreshBtn').textContent='↻';
}

async function loadAllTimeframes(){
  await loadData();
  // Load MTF in background
  const tfs=['5m','15m','1h','4h','1d','1w'];
  for(const tf of tfs){
    try{
      const{closes,highs,lows}=await fetchKlines(pair,tf,100);
      mtfData[tf]=analyze(closes,highs,lows);
    }catch(e){}
  }
  renderMTF();
}

// ── Render ────────────────────────────────────────────────
function renderSignal(){
  if(!analysis)return;
  document.getElementById('loadingMsg').style.display='none';
  document.getElementById('signalContent').style.display='block';
  const a=analysis;
  const sc=a.signal==='COMPRAR'?'#00ff88':a.signal==='VENDER'?'#ff3355':'#888';
  document.getElementById('pairLabel').textContent=`${pair.replace('USDT','/USDT')} · ${currentTF.toUpperCase()}`;
  document.getElementById('priceDisplay').textContent=`$${fp(a.price)}`;
  const sb=document.getElementById('signalBox');
  sb.style.borderColor=sc;sb.style.background=a.signal==='COMPRAR'?'#0d2d1a':a.signal==='VENDER'?'#2d0d0d':'#111';
  document.getElementById('signalIcon').textContent=a.signal==='COMPRAR'?'▲':a.signal==='VENDER'?'▼':'●';
  document.getElementById('signalIcon').style.color=sc;
  document.getElementById('signalText').textContent=a.signal;document.getElementById('signalText').style.color=sc;
  document.getElementById('directionText').textContent=a.direction;document.getElementById('directionText').style.color=sc;
  document.getElementById('confidenceText').textContent=a.confidence+'%';document.getElementById('confidenceText').style.color=sc;
  document.getElementById('bullScore').textContent=a.bullScore;
  document.getElementById('bearScore').textContent=a.bearScore;
  document.getElementById('progressFill').style.width=a.confidence+'%';document.getElementById('progressFill').style.background=sc;
  document.getElementById('entryPrice').textContent='$'+fp(a.entry);
  document.getElementById('tpPrice').textContent='$'+fp(a.tp);
  document.getElementById('slPrice').textContent='$'+fp(a.sl);
  const rrEl=document.getElementById('rrText');rrEl.textContent='1 : '+a.rr.toFixed(2);rrEl.style.color=a.rr>=1.5?'#00ff88':'#ff8c00';
  document.getElementById('posSizeText').textContent=a.posSize+' unidades';
  document.getElementById('indicatorsList').innerHTML=a.reasons.map(r=>`<div class="indicator-row"><span style="font-size:10px;color:#666">${r.l}</span><span style="font-size:10px;font-weight:600;color:${r.b===true?'#00ff88':r.b===false?'#ff3355':'#444'}">${r.v}</span></div>`).join('');
  // Alert banner
  const ab=document.getElementById('alertBanner');
  if(a.signal!=='NEUTRO'&&a.confidence>=70){
    ab.style.display='block';ab.style.background=a.signal==='COMPRAR'?'#0d2d1a':'#2d0d0d';ab.style.borderColor=sc;ab.style.color=sc;
    ab.textContent=`🔔 SEÑAL FUERTE: ${a.signal} ${pair.replace('USDT','/USDT')} — Confianza ${a.confidence}% — TF: ${currentTF.toUpperCase()}`;
  }else{ab.style.display='none'}
  renderActionArea();
  renderMTF();
}

function renderMTF(){
  const container=document.getElementById('mtfSummary');
  const consensus=document.getElementById('mtfConsensus');
  if(!container)return;
  const tfs=['5m','15m','1h','4h','1d','1w'];
  let bullCount=0,bearCount=0,html='';
  tfs.forEach(tf=>{
    const d=tf===currentTF?analysis:mtfData[tf];
    if(!d){html+=`<span class="mtf-badge" style="background:#111;color:#333">${tf.toUpperCase()}: —</span>`;return}
    const c=d.signal==='COMPRAR'?'#00ff88':d.signal==='VENDER'?'#ff3355':'#888';
    const bg=d.signal==='COMPRAR'?'#0d2d1a':d.signal==='VENDER'?'#2d0d0d':'#111';
    html+=`<span class="mtf-badge" style="background:${bg};color:${c};border:1px solid ${c}44">${tf.toUpperCase()}: ${d.signal==='COMPRAR'?'▲':d.signal==='VENDER'?'▼':'—'}</span>`;
    if(d.signal==='COMPRAR')bullCount++;
    else if(d.signal==='VENDER')bearCount++;
  });
  container.innerHTML=html;
  let msg='';
  if(bullCount>=3)msg=`✅ Consenso ALCISTA (${bullCount} TF) — señal fuerte de compra`;
  else if(bearCount>=3)msg=`✅ Consenso BAJISTA (${bearCount} TF) — señal fuerte de venta`;
  else msg=`⚠ Mercado mixto — esperar confirmación`;
  consensus.textContent=msg;
  consensus.style.color=bullCount>=3?'#00ff88':bearCount>=3?'#ff3355':'#888';
}

function renderActionArea(){
  const a=analysis;if(!a)return;
  const sc=a.signal==='COMPRAR'?'#00ff88':a.signal==='VENDER'?'#ff3355':'#888';
  const el=document.getElementById('actionArea');
  if(!openTrade){
    const disabled=a.signal==='NEUTRO';
    if (isRealMode) {
      el.innerHTML=`<div>
        <div style="display:flex;gap:6px;margin-bottom:8px">
          ${[5,10,25,50].map(p=>`<button onclick="selectedPct=${p}" style="flex:1;padding:6px 0;background:${selectedPct===p?'#1f1000':'#111'};border:1px solid ${selectedPct===p?'#ff8800':'#2a2a3e'};color:${selectedPct===p?'#ff8800':'#444'};border-radius:6px;font-size:10px;cursor:pointer">${p}%</button>`).join('')}
        </div>
        <button onclick="executeRealOrder()" ${disabled?'disabled':''} style="width:100%;padding:12px 0;font-size:12px;letter-spacing:2px;background:${disabled?'#111':a.signal==='COMPRAR'?'#0d2d1a':'#2d0d0d'};border:1px solid ${disabled?'#222':sc};color:${disabled?'#333':sc};border-radius:10px;cursor:${disabled?'not-allowed':'pointer'};font-weight:700">
          ${disabled?'SIN SEÑAL ACTIVA':'⚡ EJECUTAR '+a.signal+' REAL'}
        </button>
        <div style="font-size:9px;color:#333;text-align:center;margin-top:4px">Orden MARKET · Dinero real · Sin garantía de precio exacto</div>
      </div>`;
      return;
    }
    el.innerHTML=`<button class="btn" onclick="openPaperTrade()" ${disabled?'disabled':''} style="width:100%;padding:12px 0;font-size:12px;letter-spacing:2px;background:${disabled?'#111':a.signal==='COMPRAR'?'#0d2d1a':'#2d0d0d'};border-color:${disabled?'#222':sc};color:${disabled?'#333':sc};cursor:${disabled?'not-allowed':'pointer'}">${disabled?'SIN SEÑAL ACTIVA':'▶ SIMULAR '+a.signal+' (DEMO)'}</button>`;
  }else{
    const pricePct=openTrade.signal==='COMPRAR'?(a.price-openTrade.entry)/openTrade.entry:(openTrade.entry-a.price)/openTrade.entry;
    const pnl=openTrade.size*pricePct;
    const pnlPct=(pricePct*100).toFixed(2);
    const pnlColor=pnl>=0?'#00ff88':'#ff3355';
    el.innerHTML=`<div style="background:#0d0d18;border:1px solid ${openTrade.signal==='COMPRAR'?'#00ff8855':'#ff335555'};border-radius:10px;padding:12px">
      <div style="font-size:9px;color:#444;letter-spacing:2px;margin-bottom:6px">OPERACIÓN ABIERTA (DEMO) · ${openTrade.tf||currentTF}</div>
      <div style="display:flex;justify-content:space-between;margin-bottom:5px">
        <span style="font-size:11px;color:${openTrade.signal==='COMPRAR'?'#00ff88':'#ff3355'};font-weight:700">${openTrade.signal} · ${openTrade.direction}</span>
        <span style="font-size:11px;font-weight:700;color:${pnlColor}">${pnl>=0?'+':''}$${pnl.toFixed(2)} (${pnlPct}%)</span>
      </div>
      <div style="font-size:10px;color:#555;margin-bottom:8px">Entrada $${fp(openTrade.entry)} → Actual $${fp(a.price)} · SL $${fp(openTrade.sl)} · TP $${fp(openTrade.tp)}</div>
      <div style="display:flex;gap:5px">
        <button class="btn btn-green" onclick="closePaperTrade('TP')" style="flex:1;padding:8px 0">✓ CERRAR</button>
        <button class="btn btn-red" onclick="closePaperTrade('SL')" style="flex:1;padding:8px 0">✗ STOP</button>
        <button class="btn btn-gray" onclick="closePaperTrade('Manual')" style="flex:1;padding:8px 0">— MANUAL</button>
      </div>
    </div>`;
  }
}

// ── Paper Trading ─────────────────────────────────────────
function openPaperTrade(){
  if(!analysis||analysis.signal==='NEUTRO'||openTrade)return;
  const size=capital*0.95;
  openTrade={id:Date.now(),pair,signal:analysis.signal,direction:analysis.direction,entry:analysis.entry,tp:analysis.tp,sl:analysis.sl,qty:size/analysis.entry,size,tf:currentTF,openTime:new Date().toLocaleTimeString('es-AR'),confidence:analysis.confidence,auto:autoMode};
  _set('openTrade',JSON.stringify(openTrade));
  renderActionArea();
}

function closePaperTrade(reason){
  if(!openTrade||!analysis)return;
  const exitPrice=analysis.price;
  const pricePct=openTrade.signal==='COMPRAR'?(exitPrice-openTrade.entry)/openTrade.entry:(openTrade.entry-exitPrice)/openTrade.entry;
  const pnl=openTrade.size*pricePct;
  const pnlPct=pricePct*100;
  const closed={...openTrade,exitPrice,pnl,pnlPct,closeTime:new Date().toLocaleTimeString('es-AR'),reason};
  trades.unshift(closed);
  capital+=pnl;
  dailyPnl+=pnl;
  dailyTrades++;
  if(pnl<0)consecutiveLosses++;else consecutiveLosses=0;
  _set('capital',capital.toString());
  _set('trades',JSON.stringify(trades.slice(0,100)));
  _set('dailyPnl',dailyPnl.toString());
  _set('dailyTrades',dailyTrades.toString());
  openTrade=null;
  _set('openTrade','null');
  updateCapitalDisplay();
  renderActionArea();
  renderTrades();
  renderStats();
  updateAutoStats();
  if(consecutiveLosses>=3){
    addAutoLog('⚠ 3 pérdidas seguidas — bot pausado por tu seguridad');
    stopAuto();
  }
}

function updateCapitalDisplay(){
  const el=document.getElementById('capitalDisplay');
  el.textContent='$'+capital.toFixed(2);
  el.style.color=capital>=INITIAL_CAPITAL?'#00ff88':'#ff3355';
}

function renderTrades(){
  const el=document.getElementById('tradesList');
  if(trades.length===0){el.innerHTML='<div style="text-align:center;padding:30px;color:#333"><div style="font-size:24px;margin-bottom:8px">📋</div><div style="font-size:11px">Sin operaciones aún</div></div>';return}
  el.innerHTML=trades.map(t=>`<div class="trade-item" style="border-color:${t.pnl>=0?'#00ff8822':'#ff335522'};background:#0d0d18">
    <div style="display:flex;justify-content:space-between;margin-bottom:3px">
      <div style="display:flex;align-items:center;gap:6px">
        <span style="font-size:10px;font-weight:700;color:${t.signal==='COMPRAR'?'#00ff88':'#ff3355'}">${t.signal} · ${t.pair.replace('USDT','/USDT')} · ${t.tf||'?'}</span>
        <span style="font-size:9px;padding:2px 5px;border-radius:4px;background:${t.auto?'#1a1a3e':'#1a2a1a'};color:${t.auto?'#8888ff':'#00ff88'}">${t.auto?'🤖 AUTO':'👤 MANUAL'}</span>
      </div>
      <span style="font-size:11px;font-weight:700;color:${t.pnl>=0?'#00ff88':'#ff3355'}">${t.pnl>=0?'+':''}$${t.pnl.toFixed(2)} (${t.pnlPct>=0?'+':''}${t.pnlPct.toFixed(2)}%)</span>
    </div>
    <div style="font-size:9px;color:#333">$${fp(t.entry)} → $${fp(t.exitPrice)} · ${t.reason} · ${t.openTime} · Conf ${t.confidence}%</div>
  </div>`).join('');
}

function renderStats(){
  const totalPnl=trades.reduce((a,t)=>a+t.pnl,0);
  const wins=trades.filter(t=>t.pnl>0).length;
  const losses=trades.filter(t=>t.pnl<0).length;
  const winRate=trades.length>0?Math.round(wins/trades.length*100):0;
  const capPct=(((capital-INITIAL_CAPITAL)/INITIAL_CAPITAL)*100).toFixed(2);
  const avgWin=wins>0?trades.filter(t=>t.pnl>0).reduce((a,t)=>a+t.pnl,0)/wins:0;
  const avgLoss=losses>0?Math.abs(trades.filter(t=>t.pnl<0).reduce((a,t)=>a+t.pnl,0)/losses):0;
  const stats=[
    {l:'Capital',v:'$'+capital.toFixed(2),c:capital>=INITIAL_CAPITAL?'#00ff88':'#ff3355'},
    {l:'P&L total',v:(totalPnl>=0?'+':'')+'$'+totalPnl.toFixed(2),c:totalPnl>=0?'#00ff88':'#ff3355'},
    {l:'Win rate',v:winRate+'%',c:winRate>=50?'#00ff88':'#ff8c00'},
    {l:'Operaciones',v:trades.length,c:'#8888ff'},
    {l:'Ganadas',v:wins,c:'#00ff88'},
    {l:'Perdidas',v:losses,c:'#ff3355'},
    {l:'Promedio ganancia',v:'$'+avgWin.toFixed(2),c:'#00ff88'},
    {l:'Promedio pérdida',v:'-$'+avgLoss.toFixed(2),c:'#ff3355'},
  ];
  document.getElementById('statsGrid').innerHTML=stats.map(s=>`<div class="stat-card"><div style="font-size:9px;color:#444;margin-bottom:3px">${s.l}</div><div style="font-size:16px;font-weight:700;color:${s.c}">${s.v}</div></div>`).join('');
  // TF stats
  const tfGroups={};
  trades.forEach(t=>{const tf=t.tf||'?';if(!tfGroups[tf])tfGroups[tf]={wins:0,total:0,pnl:0};tfGroups[tf].total++;if(t.pnl>0)tfGroups[tf].wins++;tfGroups[tf].pnl+=t.pnl});
  document.getElementById('tfStats').innerHTML=Object.entries(tfGroups).map(([tf,d])=>`<div class="indicator-row"><span style="font-size:10px;color:#666">${tf.toUpperCase()}</span><span style="font-size:10px;color:#888">${d.wins}/${d.total} wins · ${(d.pnl>=0?'+':'')+'$'+d.pnl.toFixed(2)}</span></div>`).join('')||'<div style="font-size:10px;color:#333;text-align:center;padding:8px">Sin datos aún</div>';
  // Streak
  let streak=0,streakType='';
  for(const t of trades){if(t.pnl>0){if(streakType==='win')streak++;else{streak=1;streakType='win'}}else{if(streakType==='loss')streak++;else{streak=1;streakType='loss'}}break}
  document.getElementById('streakInfo').textContent=trades.length===0?'Sin operaciones aún':`Racha actual: ${streak} ${streakType==='win'?'✅ ganadas':'❌ perdidas'} seguidas`;
}

// ── Auto Trading ──────────────────────────────────────────
function toggleAutoTF(btn){btn.classList.toggle('active');const tf=btn.dataset.tf;if(btn.classList.contains('active')){if(!autoTFs.includes(tf))autoTFs.push(tf)}else{autoTFs=autoTFs.filter(t=>t!==tf)}}

function toggleAuto(){
  if(autoMode){stopAuto();return}
  autoMode=true;
  document.getElementById('autoDot').style.background='#00ff88';
  document.getElementById('autoStatusText').textContent='ACTIVO — Monitoreando...';
  document.getElementById('autoBtn').textContent='⏸ PAUSAR AUTO';
  document.getElementById('autoBtn').className='btn btn-orange';
  addAutoLog('▶ Bot automático activado');
  runAutoCheck();
  autoTimer=setInterval(runAutoCheck,60000);
}

function stopAuto(){
  autoMode=false;
  clearInterval(autoTimer);
  document.getElementById('autoDot').style.background='#333';
  document.getElementById('autoStatusText').textContent='INACTIVO';
  document.getElementById('autoBtn').textContent='▶ ACTIVAR AUTO';
  document.getElementById('autoBtn').className='btn btn-blue';
  addAutoLog('■ Bot automático detenido');
}

async function runAutoCheck(){
  if(!autoMode)return;
  const maxGain=capital*(parseFloat(document.getElementById('maxDailyGain').value)||5)/100;
  const maxLoss=capital*(parseFloat(document.getElementById('maxDailyLoss').value)||3)/100;
  const minConf=parseInt(document.getElementById('minConfidence').value)||70;
  const requireMTF=document.getElementById('requireMTF').checked;
  // Check daily limits
  if(dailyPnl>=maxGain){addAutoLog(`✅ Límite de ganancia alcanzado ($${dailyPnl.toFixed(2)})`);stopAuto();return}
  if(dailyPnl<=-maxLoss){addAutoLog(`🛑 Límite de pérdida alcanzado ($${dailyPnl.toFixed(2)})`);stopAuto();return}
  // Analyze each TF
  let signals=[];
  for(const tf of autoTFs){
    try{
      const{closes,highs,lows}=await fetchKlines(pair,tf,100);
      const a=analyze(closes,highs,lows);
      if(a)signals.push({tf,signal:a.signal,confidence:a.confidence,analysis:a});
      addAutoLog(`📊 ${tf.toUpperCase()}: ${a?.signal||'?'} (${a?.confidence||0}%)`);
    }catch(e){}
  }
  // Check consensus
  const buySignals=signals.filter(s=>s.signal==='COMPRAR'&&s.confidence>=minConf);
  const sellSignals=signals.filter(s=>s.signal==='VENDER'&&s.confidence>=minConf);
  const threshold=requireMTF?2:1;
  if(!openTrade){
    if(buySignals.length>=threshold){
      const best=buySignals.sort((a,b)=>b.confidence-a.confidence)[0];
      analysis=best.analysis;currentTF=best.tf;
      openPaperTrade();
      addAutoLog(`🟢 COMPRA automática ${pair} · TF:${best.tf} · Conf:${best.confidence}%`);
    }else if(sellSignals.length>=threshold){
      const best=sellSignals.sort((a,b)=>b.confidence-a.confidence)[0];
      analysis=best.analysis;currentTF=best.tf;
      openPaperTrade();
      addAutoLog(`🔴 VENTA automática ${pair} · TF:${best.tf} · Conf:${best.confidence}%`);
    }else{
      addAutoLog(`⏳ Sin señal suficiente — esperando...`);
    }
  }else{
    // Check if should close
    try{
      const{closes}=await fetchKlines(pair,openTrade.tf||currentTF,5);
      const currentPrice=closes[closes.length-1];
      if(openTrade.signal==='COMPRAR'&&currentPrice>=openTrade.tp){
        analysis={...analysis,price:currentPrice};closePaperTrade('TP Auto');
        addAutoLog(`✅ TP alcanzado automáticamente`);
      }else if(openTrade.signal==='COMPRAR'&&currentPrice<=openTrade.sl){
        analysis={...analysis,price:currentPrice};closePaperTrade('SL Auto');
        addAutoLog(`🛑 SL alcanzado automáticamente`);
      }else if(openTrade.signal==='VENDER'&&currentPrice<=openTrade.tp){
        analysis={...analysis,price:currentPrice};closePaperTrade('TP Auto');
        addAutoLog(`✅ TP alcanzado automáticamente`);
      }else if(openTrade.signal==='VENDER'&&currentPrice>=openTrade.sl){
        analysis={...analysis,price:currentPrice};closePaperTrade('SL Auto');
        addAutoLog(`🛑 SL alcanzado automáticamente`);
      }
    }catch(e){}
  }
  updateAutoStats();
}

function addAutoLog(msg){
  const log=document.getElementById('autoLog');
  const time=new Date().toLocaleTimeString('es-AR');
  log.innerHTML=`<div style="margin-bottom:3px"><span style="color:#333">${time}</span> ${msg}</div>`+log.innerHTML;
  if(log.children.length>20)log.removeChild(log.lastChild);
}

function updateAutoStats(){
  const maxGain=capital*(parseFloat(document.getElementById('maxDailyGain').value)||5)/100;
  const maxLoss=capital*(parseFloat(document.getElementById('maxDailyLoss').value)||3)/100;
  const pnlEl=document.getElementById('dailyPnl');
  pnlEl.textContent=(dailyPnl>=0?'+':'')+'$'+dailyPnl.toFixed(2);
  pnlEl.style.color=dailyPnl>=0?'#00ff88':'#ff3355';
  document.getElementById('dailyTrades').textContent=dailyTrades;
  document.getElementById('gainLimit').textContent='$'+maxGain.toFixed(2);
  document.getElementById('lossLimit').textContent='$'+maxLoss.toFixed(2);
}

// ── Risk Calculator ───────────────────────────────────────
function calcRisk(){
  const cap=parseFloat(document.getElementById('riskCapital').value)||1000;
  const pct=parseFloat(document.getElementById('riskPct').value)||2;
  const maxL=(cap*pct/100).toFixed(2);
  const capPerTrade=(cap*0.2).toFixed(2);
  const toBust=Math.floor(cap/(cap*pct/100));
  document.getElementById('maxLoss').textContent='$'+maxL;
  document.getElementById('capitalPerTrade').textContent='$'+capPerTrade;
  document.getElementById('tradesToBust').textContent=toBust;
  document.getElementById('riskRule').textContent=pct+'%';
  document.getElementById('dailyLossRule').textContent='$'+(cap*pct*1.5/100).toFixed(2);
  document.getElementById('dailyGainRule').textContent='$'+(cap*pct*2.5/100).toFixed(2);
  calcPositionSize();
}

function calcPositionSize(){
  const cap=parseFloat(document.getElementById('riskCapital').value)||1000;
  const pct=parseFloat(document.getElementById('riskPct').value)||2;
  const entry=parseFloat(document.getElementById('riskEntry').value);
  const sl=parseFloat(document.getElementById('riskSL').value);
  if(!entry||!sl){document.getElementById('qtyResult').textContent='—';document.getElementById('posValue').textContent='—';return}
  const riskAmt=cap*pct/100;
  const riskPerUnit=Math.abs(entry-sl);
  const qty=(riskAmt/riskPerUnit).toFixed(6);
  const posVal=(qty*entry).toFixed(2);
  document.getElementById('qtyResult').textContent=qty+' unidades';
  document.getElementById('posValue').textContent='$'+posVal;
}

// ── Export ────────────────────────────────────────────────
function exportTrades(){
  const fecha = new Date().toLocaleDateString('es-AR').replace(/\/\//g,'-');
  if(typeof XLSX === 'undefined'){alert('Cargando Excel, intentá de nuevo en 2 segundos');return;}
  const wb = XLSX.utils.book_new();
  const headers = ['Fecha','Apertura','Cierre','Par','Señal','Dirección','TF','Confianza%','Entrada$','Salida$','SL$','TP$','PnL$','PnL%','Razón','Tipo','Cap.Antes','Cap.Después'];
  let capAcum = parseFloat(_get('capital')||1000) - trades.reduce((a,t)=>a+t.pnl,0);
  const rows = [...trades].reverse().map(t=>{
    const ca=capAcum; capAcum+=t.pnl;
    return [fecha,t.openTime||'',t.closeTime||'',t.pair||'',t.signal||'',t.direction||'',t.tf||'?',
      t.confidence||0,parseFloat(t.entry?.toFixed(4)||0),parseFloat(t.exitPrice?.toFixed(4)||0),
      parseFloat(t.sl?.toFixed(4)||0),parseFloat(t.tp?.toFixed(4)||0),
      parseFloat(t.pnl?.toFixed(2)||0),parseFloat((t.pnlPct/100)?.toFixed(4)||0),
      t.reason||'',t.auto?'AUTO':'MANUAL',parseFloat(ca.toFixed(2)),parseFloat(capAcum.toFixed(2))];
  });
  const wsData = [headers,...rows];
  const ws = XLSX.utils.aoa_to_sheet(wsData);
  ws['!cols'] = [10,9,9,10,9,9,7,9,10,10,10,10,9,7,8,7,12,14].map(w=>({wch:w}));
  const wins=trades.filter(t=>t.pnl>0).length;
  const totalPnl=trades.reduce((a,t)=>a+t.pnl,0);
  const cap=parseFloat(_get('capital')||1000);
  const stats=[['ESTADÍSTICAS',''],['',''],['Capital inicial','$1,000.00'],['Capital actual','$'+cap.toFixed(2)],
    ['P&L total',(totalPnl>=0?'+':'')+'$'+totalPnl.toFixed(2)],['Rendimiento %',((totalPnl/1000)*100).toFixed(2)+'%'],
    ['Operaciones',trades.length],['Ganadas',wins],['Perdidas',trades.length-wins],
    ['Win Rate',(trades.length>0?(wins/trades.length*100).toFixed(1):0)+'%'],
    ['Mejor op','$'+(trades.length>0?Math.max(...trades.map(t=>t.pnl)).toFixed(2):'0.00')],
    ['Peor op','$'+(trades.length>0?Math.min(...trades.map(t=>t.pnl)).toFixed(2):'0.00')]];
  const ws2=XLSX.utils.aoa_to_sheet(stats);
  ws2['!cols']=[{wch:22},{wch:16}];
  XLSX.utils.book_append_sheet(wb,ws,'Trades');
  XLSX.utils.book_append_sheet(wb,ws2,'Estadísticas');
  XLSX.writeFile(wb,'trading_journal_'+fecha+'.xlsx');
}

// ── Auto Check for SL/TP ─────────────────────────────────
function checkAutoTrade(){
  if(!openTrade||!analysis)return;
  const price=analysis.price;
  if(openTrade.signal==='COMPRAR'){
    if(price>=openTrade.tp){closePaperTrade('TP Auto')}
    else if(price<=openTrade.sl){closePaperTrade('SL Auto')}
  }else if(openTrade.signal==='VENDER'){
    if(price<=openTrade.tp){closePaperTrade('TP Auto')}
    else if(price>=openTrade.sl){closePaperTrade('SL Auto')}
  }
}

// ── Navigation ────────────────────────────────────────────
function switchTab(tab){
  ['signal','auto','risk','trades','stats'].forEach((t,i)=>{
    document.getElementById('tab-'+t).style.display=t===tab?'block':'none';
    document.querySelectorAll('.tab')[i].classList.toggle('active',t===tab);
  });
  if(tab==='trades')renderTrades();
  if(tab==='stats')renderStats();
  if(tab==='auto')updateAutoStats();
  if(tab==='risk')calcRisk();
}

function setTF(tf,btn){
  currentTF=tf;
  document.querySelectorAll('.tf-btn').forEach(b=>b.classList.remove('active'));
  btn.classList.add('active');
  loadData();
}

function onPairChange(){pair=document.getElementById('pairSelect').value;loadAllTimeframes()}
function addCustomPair(){const v=document.getElementById('customPair').value.toUpperCase().replace(/\s/g,'');if(v.length>=5){pair=v;document.getElementById('customPair').value='';loadAllTimeframes()}}

function resetAll(){
  capital=INITIAL_CAPITAL;trades=[];openTrade=null;dailyPnl=0;dailyTrades=0;consecutiveLosses=0;
  _remove('capital');_remove('trades');_remove('openTrade');_remove('dailyPnl');_remove('dailyTrades');
  updateCapitalDisplay();renderTrades();renderStats();renderActionArea();updateAutoStats();stopAuto();
}

// ── Real Mode Functions ──────────────────────────────────
let selectedPct = 5;

function selectPct(btn) {
  document.querySelectorAll('#pctSelector .tf-btn').forEach(b => b.classList.remove('active'));
  btn.classList.add('active');
  selectedPct = parseInt(btn.dataset.pct);
}

function saveApiKeys() {
  const key = document.getElementById('apiKeyInput').value.trim();
  const secret = document.getElementById('apiSecretInput').value.trim();
  if (!key || !secret) { document.getElementById('connectionStatus').textContent = '⚠ Ingresá ambas claves'; return; }
  apiKey = key; apiSecret = secret;
  _set('apiKey', key); _set('apiSecret', secret);
  document.getElementById('apiKeyInput').value = '';
  document.getElementById('apiSecretInput').value = '';
  document.getElementById('connectionStatus').textContent = '✓ Claves guardadas';
  testConnection();
}

async function testConnection() {
  if (!apiKey || !apiSecret) { document.getElementById('connectionStatus').textContent = '⚠ Primero guardá las claves'; return; }
  document.getElementById('connectionStatus').textContent = '🔄 Probando conexión...';
  document.getElementById('connectionStatus').style.color = '#888';
  try {
    const data = await backendPost('/balance', {apiKey, apiSecret});
    if (data.usdt !== undefined) {
      realBalance = data.usdt;
      document.getElementById('connectionStatus').textContent = '✅ Conectado — Saldo: $' + data.usdt.toFixed(2) + ' USDT';
      document.getElementById('connectionStatus').style.color = '#00ff88';
      document.getElementById('realBalanceDisplay').textContent = '$' + data.usdt.toFixed(2) + ' USDT';
      document.getElementById('realBalanceDisplay').style.display = 'block';
    } else {
      document.getElementById('connectionStatus').textContent = '✗ Error: ' + (data.error || 'Clave inválida');
      document.getElementById('connectionStatus').style.color = '#ff3355';
    }
  } catch(e) {
    document.getElementById('connectionStatus').textContent = '✗ Sin conexión al backend';
    document.getElementById('connectionStatus').style.color = '#ff3355';
  }
}

function toggleRealMode() {
  isRealMode = document.getElementById('realModeToggle').checked;
  const dot = document.getElementById('modeDot');
  const label = document.getElementById('modeLabel');
  const indicator = document.getElementById('modeIndicator');
  if (isRealMode) {
    if (!apiKey || !apiSecret) { 
      document.getElementById('realModeToggle').checked = false;
      isRealMode = false;
      alert('Primero guardá tus claves de Binance');
      return;
    }
    dot.style.background = '#ff8800'; dot.style.boxShadow = '0 0 8px #ff8800';
    label.textContent = 'REAL'; label.style.color = '#ff8800'; label.style.borderColor = '#ff880044';
    indicator.textContent = 'REAL · SALDO BINANCE';
    fetchRealBalance();
  } else {
    dot.style.background = '#00ff88'; dot.style.boxShadow = '0 0 8px #00ff88';
    label.textContent = 'DEMO'; label.style.color = '#00ff8866'; label.style.borderColor = '#00ff8822';
    indicator.textContent = 'DEMO · CAPITAL';
    document.getElementById('realBalanceDisplay').style.display = 'none';
  }
}

async function executeRealOrder() {
  if (!analysis || analysis.signal === 'NEUTRO') return;
  if (!apiKey || !apiSecret) { alert('Configurá las claves de Binance primero'); return; }
  if (!realBalance) { await fetchRealBalance(); }
  const side = analysis.signal === 'COMPRAR' ? 'BUY' : 'SELL';
  const budget = realBalance * selectedPct / 100;
  const qty = (budget / analysis.price).toFixed(5);
  if (!confirm(`⚠ ORDEN REAL\n${side} ${qty} ${pair.replace('USDT','')}\nPrecio: ~$${analysis.price}\nValor: ~$${budget.toFixed(2)} USDT\n\n¿Confirmar?`)) return;
  const result = await placeRealOrder(side, qty);
  if (result.success) {
    alert(`✅ Orden ejecutada\nID: ${result.orderId}\nCantidad: ${result.executedQty}`);
    fetchRealBalance();
  } else {
    alert(`✗ Error: ${result.error}`);
  }
}

// ── Init ──────────────────────────────────────────────────
updateCapitalDisplay();
calcRisk();
updateAutoStats();
loadAllTimeframes();
setInterval(loadData,60000);
</script>
</body>
</html>
