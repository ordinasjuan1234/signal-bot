<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Signal Bot</title>
<style>
* { box-sizing: border-box; margin: 0; padding: 0; }
body { background: #0a0a0f; color: #e0e0e0; font-family: 'Courier New', monospace; min-height: 100vh; }
.header { background: #0d0d18; border-bottom: 1px solid #1e1e2e; padding: 12px 16px; display: flex; align-items: center; justify-content: space-between; }
.dot { width: 8px; height: 8px; background: #00ff88; border-radius: 50%; box-shadow: 0 0 8px #00ff88; display: inline-block; margin-right: 8px; }
.title { font-weight: 800; font-size: 14px; letter-spacing: 3px; color: #fff; }
.badge { font-size: 9px; color: #00ff8866; border: 1px solid #00ff8822; border-radius: 4px; padding: 1px 6px; margin-left: 8px; }
.capital { text-align: right; }
.capital-label { font-size: 9px; color: #444; }
.capital-value { font-size: 13px; font-weight: 700; }
.tabs { display: flex; border-bottom: 1px solid #1e1e2e; }
.tab { flex: 1; background: none; border: none; border-bottom: 2px solid transparent; color: #444; padding: 10px 0; font-size: 10px; cursor: pointer; font-family: inherit; letter-spacing: 2px; }
.tab.active { border-bottom-color: #6666ff; color: #8888ff; }
.content { padding: 14px 14px 32px; }
.controls { display: flex; gap: 6px; margin-bottom: 14px; flex-wrap: wrap; }
select, input { background: #111; border: 1px solid #2a2a3e; color: #e0e0e0; border-radius: 8px; padding: 7px 10px; font-size: 11px; font-family: inherit; }
select { flex: 1; min-width: 120px; }
input { flex: 1; min-width: 120px; }
.interval-btn { background: #111; border: 1px solid #2a2a3e; color: #555; border-radius: 8px; padding: 7px 10px; font-size: 10px; cursor: pointer; font-family: inherit; }
.interval-btn.active { background: #1e1e3e; border-color: #6666ff; color: #8888ff; }
.refresh-btn { background: #0d1f0d; border: 1px solid #00ff8833; color: #00ff88; border-radius: 8px; padding: 7px 14px; font-size: 12px; cursor: pointer; }
.card { background: #0d0d18; border: 1px solid #1e1e2e; border-radius: 12px; padding: 14px; margin-bottom: 10px; }
.card-label { font-size: 9px; color: #333; letter-spacing: 2px; margin-bottom: 8px; }
.signal-row { display: flex; align-items: center; gap: 10px; margin-bottom: 12px; }
.signal-box { border-radius: 10px; padding: 8px 20px; text-align: center; border-width: 2px; border-style: solid; }
.signal-icon { font-size: 22px; }
.signal-text { font-size: 18px; font-weight: 800; letter-spacing: 2px; }
.signal-side { flex: 1; }
.mini-card { background: #111; border-radius: 8px; padding: 8px 12px; text-align: center; margin-bottom: 6px; }
.mini-label { font-size: 9px; color: #444; margin-bottom: 2px; }
.mini-value { font-size: 14px; font-weight: 700; }
.score-row { display: flex; gap: 6px; margin-bottom: 10px; }
.score-card { flex: 1; background: #111; border-radius: 8px; padding: 8px; text-align: center; }
.progress-bar { height: 5px; background: #222; border-radius: 3px; overflow: hidden; }
.progress-fill { height: 100%; border-radius: 3px; transition: width 0.6s; }
.levels-grid { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 6px; margin-bottom: 8px; }
.level-card { border-radius: 8px; padding: 8px; text-align: center; }
.level-label { font-size: 9px; margin-bottom: 3px; }
.level-value { font-size: 11px; font-weight: 700; }
.rr-row { display: flex; justify-content: space-between; padding: 5px 0; border-top: 1px solid #111; }
.indicator-row { display: flex; justify-content: space-between; padding: 5px 0; border-bottom: 1px solid #111; }
.indicator-label { font-size: 10px; color: #666; }
.indicator-value { font-size: 10px; font-weight: 600; }
.action-btn { width: 100%; padding: 13px 0; border-radius: 10px; font-size: 13px; font-weight: 700; cursor: pointer; font-family: inherit; letter-spacing: 2px; border-style: solid; border-width: 1px; }
.open-trade { border-radius: 12px; padding: 12px; border-style: solid; border-width: 1px; }
.close-row { display: flex; gap: 6px; margin-top: 8px; }
.close-btn { flex: 1; padding: 9px 0; border-radius: 8px; font-size: 10px; cursor: pointer; font-family: inherit; border-style: solid; border-width: 1px; }
.trade-item { border-radius: 10px; padding: 10px; margin-bottom: 8px; border-style: solid; border-width: 1px; }
.trade-header { display: flex; justify-content: space-between; margin-bottom: 4px; }
.trade-detail { font-size: 9px; color: #333; }
.stats-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 8px; margin-bottom: 10px; }
.stat-card { background: #0d0d18; border: 1px solid #1e1e2e; border-radius: 10px; padding: 10px; }
.stat-label { font-size: 9px; color: #444; margin-bottom: 4px; }
.stat-value { font-size: 18px; font-weight: 700; }
.reset-btn { width: 100%; padding: 9px 0; background: #111; border: 1px solid #2a2a3e; border-radius: 8px; color: #333; font-size: 10px; cursor: pointer; font-family: inherit; }
.disclaimer { margin-top: 12px; padding: 8px 10px; background: #0d0d0d; border-radius: 8px; font-size: 9px; color: #1e1e1e; line-height: 1.6; }
.pair-label { font-size: 10px; color: #333; letter-spacing: 2px; }
.price-display { font-size: 22px; font-weight: 800; color: #fff; margin-bottom: 10px; }
.empty-state { text-align: center; padding: 40px; color: #333; }
.empty-icon { font-size: 28px; margin-bottom: 8px; }
.empty-text { font-size: 11px; }
.loading { text-align: center; padding: 40px; color: #444; font-size: 12px; }
.error-box { background: #1a0808; border: 1px solid #ff3355; border-radius: 10px; padding: 12px; color: #ff3355; font-size: 11px; margin-bottom: 10px; }
</style>
</head>
<body>

<div class="header">
  <div style="display:flex;align-items:center">
    <span class="dot"></span>
    <span class="title">SIGNAL BOT</span>
    <span class="badge">DEMO</span>
  </div>
  <div class="capital">
    <div class="capital-label">CAPITAL DEMO</div>
    <div class="capital-value" id="capitalDisplay" style="color:#00ff88">$1000.00</div>
  </div>
</div>

<div class="tabs">
  <button class="tab active" onclick="switchTab('signal')">SEÑAL</button>
  <button class="tab" onclick="switchTab('trades')">TRADES</button>
  <button class="tab" onclick="switchTab('stats')">STATS</button>
</div>

<div class="content">
  <div class="controls">
    <select id="pairSelect" onchange="onPairChange()">
      <option value="BTCUSDT">BTC/USDT</option>
      <option value="ETHUSDT">ETH/USDT</option>
      <option value="BNBUSDT">BNB/USDT</option>
      <option value="SOLUSDT">SOL/USDT</option>
      <option value="XRPUSDT">XRP/USDT</option>
      <option value="DOGEUSDT">DOGE/USDT</option>
      <option value="ADAUSDT">ADA/USDT</option>
      <option value="AVAXUSDT">AVAX/USDT</option>
    </select>
    <input id="customPair" placeholder="Otro par..." onkeydown="if(event.key==='Enter') addCustomPair()" />
    <button onclick="addCustomPair()" style="background:#1a1a3e;border:1px solid #3333aa;color:#8888ff;border-radius:8px;padding:7px 12px;font-size:12px;cursor:pointer">+</button>
    <div style="display:flex;gap:3px">
      <button class="interval-btn active" onclick="setInterval2('15m',this)">15m</button>
      <button class="interval-btn" onclick="setInterval2('1h',this)">1h</button>
      <button class="interval-btn" onclick="setInterval2('4h',this)">4h</button>
      <button class="interval-btn" onclick="setInterval2('1d',this)">1d</button>
    </div>
    <button class="refresh-btn" onclick="loadData()" id="refreshBtn">↻</button>
  </div>

  <div id="errorBox" style="display:none" class="error-box"></div>

  <!-- SIGNAL TAB -->
  <div id="tab-signal">
    <div id="loadingMsg" class="loading">Analizando mercado...</div>
    <div id="signalContent" style="display:none">
      <div class="pair-label" id="pairLabel"></div>
      <div class="price-display" id="priceDisplay"></div>

      <div class="card" id="signalCard">
        <div class="signal-row">
          <div class="signal-box" id="signalBox">
            <div class="signal-icon" id="signalIcon"></div>
            <div class="signal-text" id="signalText"></div>
          </div>
          <div class="signal-side">
            <div class="mini-card">
              <div class="mini-label">DIRECCIÓN</div>
              <div class="mini-value" id="directionText"></div>
            </div>
            <div class="mini-card">
              <div class="mini-label">CONFIANZA</div>
              <div class="mini-value" id="confidenceText"></div>
            </div>
          </div>
        </div>
        <div class="score-row">
          <div class="score-card">
            <div class="mini-label">ALCISTAS</div>
            <div style="font-size:18px;font-weight:700;color:#00ff88" id="bullScore">0</div>
          </div>
          <div class="score-card">
            <div class="mini-label">BAJISTAS</div>
            <div style="font-size:18px;font-weight:700;color:#ff3355" id="bearScore">0</div>
          </div>
        </div>
        <div class="progress-bar"><div class="progress-fill" id="progressFill"></div></div>
      </div>

      <div class="card">
        <div class="card-label">NIVELES DE OPERACIÓN</div>
        <div class="levels-grid">
          <div class="level-card" style="background:#111">
            <div class="level-label" style="color:#555">ENTRADA</div>
            <div class="level-value" style="color:#fff" id="entryPrice"></div>
          </div>
          <div class="level-card" style="background:#0d2d1a;border:1px solid #00ff8833">
            <div class="level-label" style="color:#00ff8877">TAKE PROFIT</div>
            <div class="level-value" style="color:#00ff88" id="tpPrice"></div>
          </div>
          <div class="level-card" style="background:#2d0d0d;border:1px solid #ff335533">
            <div class="level-label" style="color:#ff335577">STOP LOSS</div>
            <div class="level-value" style="color:#ff3355" id="slPrice"></div>
          </div>
        </div>
        <div class="rr-row">
          <span style="font-size:10px;color:#444">Ratio R/R</span>
          <span style="font-size:11px;font-weight:700" id="rrText"></span>
        </div>
      </div>

      <div class="card">
        <div class="card-label">INDICADORES</div>
        <div id="indicatorsList"></div>
      </div>

      <div id="actionArea"></div>
    </div>
  </div>

  <!-- TRADES TAB -->
  <div id="tab-trades" style="display:none">
    <div class="card-label">HISTORIAL DE OPERACIONES DEMO</div>
    <div id="tradesList"></div>
  </div>

  <!-- STATS TAB -->
  <div id="tab-stats" style="display:none">
    <div class="card-label" style="margin-bottom:10px">ESTADÍSTICAS DEMO</div>
    <div class="stats-grid" id="statsGrid"></div>
    <button class="reset-btn" onclick="resetDemo()">↺ Reiniciar demo</button>
  </div>

  <div class="disclaimer">MODO DEMO — Sin dinero real. Señales orientativas, no constituyen consejo financiero.</div>
</div>

<script>
let pair = 'BTCUSDT';
let interval = '15m';
let analysis = null;
let capital = parseFloat(localStorage.getItem("capital")) || 1000;
let openTrade = JSON.parse(localStorage.getItem("openTrade")) || null;
let trades = JSON.parse(localStorage.getItem("trades")) || [];
let refreshTimer = null;
const INITIAL_CAPITAL = 1000;

function fp(p) {
  if (!p && p !== 0) return '-';
  if (p > 1000) return p.toLocaleString('es-AR', {maximumFractionDigits:2});
  if (p > 1) return p.toFixed(4);
  return p.toFixed(6);
}

function calcEMA(data, period) {
  if (data.length < period) return null;
  const k = 2 / (period + 1);
  let ema = data.slice(0, period).reduce((a,b) => a+b, 0) / period;
  for (let i = period; i < data.length; i++) ema = data[i]*k + ema*(1-k);
  return ema;
}
function calcSMA(data, period) {
  if (data.length < period) return null;
  return data.slice(-period).reduce((a,b) => a+b, 0) / period;
}
function calcRSI(closes, period=14) {
  if (closes.length < period+1) return null;
  let gains=0, losses=0;
  for (let i=closes.length-period; i<closes.length; i++) {
    const d = closes[i]-closes[i-1];
    if (d>=0) gains+=d; else losses-=d;
  }
  const ag=gains/period, al=losses/period;
  if (al===0) return 100;
  return 100 - 100/(1+ag/al);
}
function calcMACD(closes) {
  if (closes.length < 26) return null;
  const e12=calcEMA(closes,12), e26=calcEMA(closes,26);
  if (!e12||!e26) return null;
  return {macdLine: e12-e26};
}
function calcBB(closes, period=20) {
  if (closes.length < period) return null;
  const slice=closes.slice(-period);
  const mean=slice.reduce((a,b)=>a+b,0)/period;
  const std=Math.sqrt(slice.reduce((a,b)=>a+Math.pow(b-mean,2),0)/period);
  return {upper:mean+2*std, middle:mean, lower:mean-2*std};
}
function analyze(closes, highs, lows) {
  if (!closes || closes.length < 30) return null;
  const price=closes[closes.length-1];
  const rsi=calcRSI(closes);
  const macd=calcMACD(closes);
  const bb=calcBB(closes);
  const sma20=calcSMA(closes,20);
  const ema50=calcEMA(closes,Math.min(50,closes.length));
  const sma200=closes.length>=200?calcSMA(closes,200):null;
  let bull=0, bear=0;
  const reasons=[];
  if (rsi!==null) {
    if (rsi<35){bull+=2;reasons.push({l:'RSI sobrevendido',v:rsi.toFixed(1),b:true});}
    else if (rsi>65){bear+=2;reasons.push({l:'RSI sobrecomprado',v:rsi.toFixed(1),b:false});}
    else reasons.push({l:'RSI neutro',v:rsi.toFixed(1),b:null});
  }
  if (macd) {
    if (macd.macdLine>0){bull+=1;reasons.push({l:'MACD positivo',v:macd.macdLine.toFixed(4),b:true});}
    else{bear+=1;reasons.push({l:'MACD negativo',v:macd.macdLine.toFixed(4),b:false});}
  }
  if (bb) {
    if (price<bb.lower){bull+=2;reasons.push({l:'Bajo banda inferior BB',v:price.toFixed(2),b:true});}
    else if (price>bb.upper){bear+=2;reasons.push({l:'Sobre banda superior BB',v:price.toFixed(2),b:false});}
    else reasons.push({l:'Dentro de Bollinger',v:`${bb.lower.toFixed(2)}–${bb.upper.toFixed(2)}`,b:null});
  }
  if (sma20){
    if (price>sma20){bull+=1;reasons.push({l:'Precio > SMA20',v:sma20.toFixed(2),b:true});}
    else{bear+=1;reasons.push({l:'Precio < SMA20',v:sma20.toFixed(2),b:false});}
  }
  if (ema50){
    if (price>ema50){bull+=1;reasons.push({l:'Precio > EMA50',v:ema50.toFixed(2),b:true});}
    else{bear+=1;reasons.push({l:'Precio < EMA50',v:ema50.toFixed(2),b:false});}
  }
  if (sma200){
    if (price>sma200){bull+=1;reasons.push({l:'Tendencia alcista SMA200',v:sma200.toFixed(2),b:true});}
    else{bear+=1;reasons.push({l:'Tendencia bajista SMA200',v:sma200.toFixed(2),b:false});}
  }
  const total=bull+bear;
  const conf=total>0?Math.round((Math.max(bull,bear)/total)*100):50;
  const diff=bull-bear;
  let signal,direction;
  if (diff>=2){signal='COMPRAR';direction='LARGO';}
  else if (diff<=-2){signal='VENDER';direction='SHORT';}
  else{signal='NEUTRO';direction='ESPERAR';}
  const rHigh=Math.max(...highs.slice(-14));
  const rLow=Math.min(...lows.slice(-14));
  const range=rHigh-rLow;
  let entry=price,tp,sl;
  if (signal==='COMPRAR'){tp=price+range*0.618;sl=price-range*0.382;}
  else if (signal==='VENDER'){tp=price-range*0.618;sl=price+range*0.382;}
  else{tp=price+range*0.3;sl=price-range*0.3;}
  const rr=Math.abs(tp-entry)/Math.abs(sl-entry);
  return {signal,direction,confidence:conf,price,entry,tp,sl,rr,reasons,bullScore:bull,bearScore:bear};
}

async function loadData() {
  document.getElementById('loadingMsg').style.display='block';
  document.getElementById('signalContent').style.display='none';
  document.getElementById('errorBox').style.display='none';
  document.getElementById('refreshBtn').textContent='...';
  try {
    const res = await fetch(`https://api.binance.com/api/v3/klines?symbol=${pair}&interval=${interval}&limit=200`);
    if (!res.ok) throw new Error('Par no encontrado');
    const data = await res.json();
    const closes=data.map(k=>parseFloat(k[4]));
    const highs=data.map(k=>parseFloat(k[2]));
    const lows=data.map(k=>parseFloat(k[3]));
    analysis = analyze(closes,highs,lows);
    renderSignal();
  } catch(e) {
    document.getElementById('errorBox').textContent='⚠ '+e.message;
    document.getElementById('errorBox').style.display='block';
    document.getElementById('loadingMsg').style.display='none';
  }
  document.getElementById('refreshBtn').textContent='↻';
}

function renderSignal() {
  if (!analysis) return;
  document.getElementById('loadingMsg').style.display='none';
  document.getElementById('signalContent').style.display='block';
  const a=analysis;
  const sc = a.signal==='COMPRAR'?'#00ff88':a.signal==='VENDER'?'#ff3355':'#888';
  document.getElementById('pairLabel').textContent=`${pair.replace('USDT','')} / USDT · ${interval.toUpperCase()}`;
  document.getElementById('priceDisplay').textContent=`$${fp(a.price)}`;
  const sb=document.getElementById('signalBox');
  sb.style.borderColor=sc;
  sb.style.background=a.signal==='COMPRAR'?'#0d2d1a':a.signal==='VENDER'?'#2d0d0d':'#111';
  document.getElementById('signalIcon').textContent=a.signal==='COMPRAR'?'▲':a.signal==='VENDER'?'▼':'●';
  document.getElementById('signalIcon').style.color=sc;
  document.getElementById('signalText').textContent=a.signal;
  document.getElementById('signalText').style.color=sc;
  document.getElementById('directionText').textContent=a.direction;
  document.getElementById('directionText').style.color=sc;
  document.getElementById('confidenceText').textContent=a.confidence+'%';
  document.getElementById('confidenceText').style.color=sc;
  document.getElementById('bullScore').textContent=a.bullScore;
  document.getElementById('bearScore').textContent=a.bearScore;
  document.getElementById('progressFill').style.width=a.confidence+'%';
  document.getElementById('progressFill').style.background=sc;
  document.getElementById('entryPrice').textContent='$'+fp(a.entry);
  document.getElementById('tpPrice').textContent='$'+fp(a.tp);
  document.getElementById('slPrice').textContent='$'+fp(a.sl);
  const rrEl=document.getElementById('rrText');
  rrEl.textContent='1 : '+a.rr.toFixed(2);
  rrEl.style.color=a.rr>=1.5?'#00ff88':'#ff8c00';
  const il=document.getElementById('indicatorsList');
  il.innerHTML=a.reasons.map(r=>`<div class="indicator-row"><span class="indicator-label">${r.l}</span><span class="indicator-value" style="color:${r.b===true?'#00ff88':r.b===false?'#ff3355':'#444'}">${r.v}</span></div>`).join('');
  renderActionArea();
}

function renderActionArea() {
  const a=analysis;
  if (!a) return;
  const sc=a.signal==='COMPRAR'?'#00ff88':a.signal==='VENDER'?'#ff3355':'#888';
  const el=document.getElementById('actionArea');
  if (!openTrade) {
    const disabled=a.signal==='NEUTRO';
    el.innerHTML=`<button class="action-btn" onclick="openPaperTrade()" ${disabled?'disabled':''} style="background:${disabled?'#111':a.signal==='COMPRAR'?'#0d2d1a':'#2d0d0d'};border-color:${disabled?'#222':sc};color:${disabled?'#333':sc};cursor:${disabled?'not-allowed':'pointer'}">${disabled?'SIN SEÑAL ACTIVA':'▶ SIMULAR '+a.signal+' (DEMO)'}</button>`;
  } else {
    const pricePct2 = openTrade.signal==='COMPRAR' ? (a.price-openTrade.entry)/openTrade.entry : (openTrade.entry-a.price)/openTrade.entry;
    const pnl = openTrade.size * pricePct2;
    const pnlPct=(pricePct2*100).toFixed(2);
    const pnlColor=pnl>=0?'#00ff88':'#ff3355';
    const bc=openTrade.signal==='COMPRAR'?'#00ff8855':'#ff335555';
    el.innerHTML=`<div class="open-trade" style="border-color:${bc};background:#0d0d18">
      <div class="card-label">OPERACIÓN ABIERTA (DEMO)</div>
      <div style="display:flex;justify-content:space-between;margin-bottom:6px">
        <span style="font-size:11px;color:${openTrade.signal==='COMPRAR'?'#00ff88':'#ff3355'};font-weight:700">${openTrade.signal} · ${openTrade.direction}</span>
        <span style="font-size:10px;color:#444">${openTrade.openTime}</span>
      </div>
      <div style="font-size:10px;color:#555;margin-bottom:8px">Entrada $${fp(openTrade.entry)} → Actual $${fp(a.price)} <span style="color:${pnlColor};font-weight:700">${pnl>=0?'+':''}${pnlPct}%</span></div>
      <div class="close-row">
        <button class="close-btn" onclick="closePaperTrade('TP')" style="background:#0d2d1a;border-color:#00ff88;color:#00ff88">✓ CERRAR</button>
        <button class="close-btn" onclick="closePaperTrade('SL')" style="background:#2d0d0d;border-color:#ff3355;color:#ff3355">✗ STOP</button>
        <button class="close-btn" onclick="closePaperTrade('Manual')" style="background:#111;border-color:#333;color:#555">— MANUAL</button>
      </div>
    </div>`;
  }
}

function openPaperTrade() {
  if (!analysis||analysis.signal==='NEUTRO'||openTrade) return;
  const size=capital*0.95;
  openTrade={id:Date.now(),pair,signal:analysis.signal,direction:analysis.direction,entry:analysis.entry,tp:analysis.tp,sl:analysis.sl,qty:size/analysis.entry,size,openTime:new Date().toLocaleTimeString('es-AR'),confidence:analysis.confidence};
  localStorage.setItem("openTrade", JSON.stringify(openTrade));
  renderActionArea();
}

function closePaperTrade(reason) {
  if (!openTrade||!analysis) return;
  const exitPrice=analysis.price;
  const pricePct = openTrade.signal==='COMPRAR' ? (exitPrice-openTrade.entry)/openTrade.entry : (openTrade.entry-exitPrice)/openTrade.entry;
  const pnl = openTrade.size * pricePct;
  const pnlPct = pricePct * 100;
  trades.unshift({...openTrade,exitPrice,pnl,pnlPct,closeTime:new Date().toLocaleTimeString('es-AR'),reason});
  capital+=pnl; localStorage.setItem("capital", capital); localStorage.setItem("trades", JSON.stringify(trades)); localStorage.setItem("openTrade", null);
  openTrade=null; localStorage.setItem("openTrade", null);
  updateCapitalDisplay();
  renderActionArea();
  renderTrades();
  renderStats();
}

function updateCapitalDisplay() {
  const el=document.getElementById('capitalDisplay');
  el.textContent='$'+capital.toFixed(2);
  el.style.color=capital>=INITIAL_CAPITAL?'#00ff88':'#ff3355';
}

function renderTrades() {
  const el=document.getElementById('tradesList');
  if (trades.length===0) {
    el.innerHTML='<div class="empty-state"><div class="empty-icon">📋</div><div class="empty-text">Sin operaciones cerradas aún</div></div>';
    return;
  }
  el.innerHTML=trades.map(t=>`<div class="trade-item" style="border-color:${t.pnl>=0?'#00ff8822':'#ff335522'};background:#0d0d18">
    <div class="trade-header">
      <span style="font-size:11px;font-weight:700;color:${t.signal==='COMPRAR'?'#00ff88':'#ff3355'}">${t.signal} · ${t.pair.replace('USDT','/USDT')}</span>
      <span style="font-size:12px;font-weight:700;color:${t.pnl>=0?'#00ff88':'#ff3355'}">${t.pnl>=0?'+':''}$${t.pnl.toFixed(2)} (${t.pnlPct>=0?'+':''}${t.pnlPct.toFixed(2)}%)</span>
    </div>
    <div class="trade-detail">$${fp(t.entry)} → $${fp(t.exitPrice)} · ${t.reason} · ${t.openTime}</div>
  </div>`).join('');
}

function renderStats() {
  const totalPnl=trades.reduce((a,t)=>a+t.pnl,0);
  const wins=trades.filter(t=>t.pnl>0).length;
  const winRate=trades.length>0?Math.round(wins/trades.length*100):0;
  const capPct=(((capital-INITIAL_CAPITAL)/INITIAL_CAPITAL)*100).toFixed(2);
  const stats=[
    {l:'Capital actual',v:'$'+capital.toFixed(2),c:capital>=INITIAL_CAPITAL?'#00ff88':'#ff3355'},
    {l:'P&L total',v:(totalPnl>=0?'+':'')+'$'+totalPnl.toFixed(2),c:totalPnl>=0?'#00ff88':'#ff3355'},
    {l:'Win rate',v:winRate+'%',c:winRate>=50?'#00ff88':'#ff8c00'},
    {l:'Operaciones',v:trades.length,c:'#8888ff'},
    {l:'Ganadas',v:wins,c:'#00ff88'},
    {l:'Perdidas',v:trades.length-wins,c:'#ff3355'},
  ];
  document.getElementById('statsGrid').innerHTML=stats.map(s=>`<div class="stat-card"><div class="stat-label">${s.l}</div><div class="stat-value" style="color:${s.c}">${s.v}</div></div>`).join('');
}

function resetDemo() {
  capital=INITIAL_CAPITAL; localStorage.removeItem("capital"); localStorage.removeItem("trades"); localStorage.removeItem("openTrade");
  trades=[];
  openTrade=null; localStorage.setItem("openTrade", null);
  updateCapitalDisplay();
  renderTrades();
  renderStats();
  renderActionArea();
}

function switchTab(tab) {
  ['signal','trades','stats'].forEach(t=>{
    document.getElementById('tab-'+t).style.display=t===tab?'block':'none';
    document.querySelector(`.tab:nth-child(${['signal','trades','stats'].indexOf(t)+1})`).classList.toggle('active',t===tab);
  });
  if (tab==='trades') renderTrades();
  if (tab==='stats') renderStats();
}

function setInterval2(val, btn) {
  interval=val;
  document.querySelectorAll('.interval-btn').forEach(b=>b.classList.remove('active'));
  btn.classList.add('active');
  loadData();
}

function onPairChange() {
  pair=document.getElementById('pairSelect').value;
  loadData();
}

function addCustomPair() {
  const val=document.getElementById('customPair').value.toUpperCase().replace(/\s/g,'');
  if (val.length>=5) {
    pair=val;
    document.getElementById('customPair').value='';
    loadData();
  }
}

// Init
loadData();
setInterval(loadData, 60000);
renderTrades();
renderStats();
</script>
</body>
</html>
