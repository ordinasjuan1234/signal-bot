process.on('uncaughtException', (e) => { console.error('UNCAUGHT:', e); });
process.on('unhandledRejection', (e) => { console.error('UNHANDLED:', e); });

const express = require("express");
const cors = require("cors");
const crypto = require("crypto");

const app = express();
app.use(cors());
app.use(express.json());

const PORT = process.env.PORT || 3001;
const TELEGRAM_TOKEN = process.env.TELEGRAM_TOKEN;
const TELEGRAM_CHAT_ID = process.env.TELEGRAM_CHAT_ID;
const WEBHOOK_SECRET = process.env.WEBHOOK_SECRET || "signalbot2024";

function hmac(secret, message) {
  return crypto.createHmac("sha256", secret).update(message).digest("hex");
}

async function sendTelegram(message) {
  if (!TELEGRAM_TOKEN || !TELEGRAM_CHAT_ID) { console.log('Telegram no configurado'); return; }
  try {
    const r = await fetch(`https://api.telegram.org/bot${TELEGRAM_TOKEN}/sendMessage`, {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({ chat_id: TELEGRAM_CHAT_ID, text: message, parse_mode: "HTML" })
    });
    const d = await r.json();
    console.log('Telegram:', d.ok ? 'OK' : d.description);
  } catch (e) { console.log("Telegram error:", e.message); }
}

// ── Health check ──────────────────────────────────────────
app.get("/", (req, res) => {
  res.json({ status: "Signal Bot Backend OK", time: new Date().toISOString() });
});

// ── Balance ───────────────────────────────────────────────
app.post("/balance", async (req, res) => {
  const { apiKey, apiSecret } = req.body;
  if (!apiKey || !apiSecret) return res.status(400).json({ error: "Faltan claves" });
  try {
    const timestamp = Date.now();
    const query = `timestamp=${timestamp}`;
    const signature = hmac(apiSecret, query);
    const response = await fetch(`https://api.binance.com/api/v3/account?${query}&signature=${signature}`, {
      headers: { "X-MBX-APIKEY": apiKey }
    });
    const data = await response.json();
    if (data.code) return res.status(400).json({ error: data.msg });
    const usdt = data.balances?.find(b => b.asset === "USDT");
    res.json({ usdt: usdt ? parseFloat(usdt.free) : 0 });
  } catch (e) { res.status(500).json({ error: e.message }); }
});

// ── Order ─────────────────────────────────────────────────
app.post("/order", async (req, res) => {
  const { apiKey, apiSecret, symbol, side, quantity, type = "MARKET" } = req.body;
  if (!apiKey || !apiSecret || !symbol || !side || !quantity) {
    return res.status(400).json({ error: "Faltan parámetros" });
  }
  try {
    const timestamp = Date.now();
    const params = `symbol=${symbol}&side=${side}&type=${type}&quantity=${quantity}&timestamp=${timestamp}`;
    const signature = hmac(apiSecret, params);
    const response = await fetch(`https://api.binance.com/api/v3/order`, {
      method: "POST",
      headers: { "X-MBX-APIKEY": apiKey, "Content-Type": "application/x-www-form-urlencoded" },
      body: `${params}&signature=${signature}`
    });
    const data = await response.json();
    if (data.code) return res.status(400).json({ error: data.msg });
    const msg = `🔔 ORDEN EJECUTADA\n${symbol} ${side}\nCantidad: ${data.executedQty}`;
    await sendTelegram(msg);
    res.json({ success: true, orderId: data.orderId, executedQty: data.executedQty, price: data.fills?.[0]?.price });
  } catch (e) { res.status(500).json({ error: e.message }); }
});

// ── Alert ─────────────────────────────────────────────────
app.post("/alert", async (req, res) => {
  const { message } = req.body;
  if (!message) return res.status(400).json({ error: "Falta mensaje" });
  await sendTelegram(message);
  res.json({ success: true });
});

// ── Daily summary endpoint ────────────────────────────────
app.post("/daily-summary", async (req, res) => {
  const { capital, dailyPnl, dailyTrades, wins, losses, winRate } = req.body;
  const now = new Date().toLocaleString('es-AR', {timeZone:'America/Argentina/Buenos_Aires'});
  let motivacion = '';
  if (dailyPnl > 0 && winRate >= 60) motivacion = '🚀 Excelente día! Seguí así, campeón!';
  else if (dailyPnl > 0) motivacion = '🟢 Buen día! De a poco se llega lejos.';
  else if (dailyPnl < 0 && losses >= 3) motivacion = '💪 Dale vos podés! Mañana es otro día.';
  else if (dailyPnl < 0) motivacion = '🔴 Día difícil. Revisá las señales y descansá.';
  else motivacion = '⚪ Día tranquilo. El mercado espera su momento.';
  
  const msg = `📊 <b>RESUMEN DIARIO</b>\n📅 ${now}\n\n💰 Capital: $${parseFloat(capital).toFixed(2)}\n📈 P&L hoy: ${dailyPnl >= 0 ? '+' : ''}$${parseFloat(dailyPnl).toFixed(2)}\n🎯 Operaciones: ${dailyTrades}\n✅ Ganadas: ${wins}\n❌ Perdidas: ${losses}\n📊 Win Rate: ${winRate}%\n\n${motivacion}`;
  await sendTelegram(msg);
  res.json({ success: true });
});

// ── Scheduled daily summary (22hs Argentina = 01hs UTC) ───
function scheduleDailySummary() {
  const now = new Date();
  const next = new Date();
  next.setUTCHours(1, 0, 0, 0); // 22hs Argentina = 01hs UTC
  if (next <= now) next.setUTCDate(next.getUTCDate() + 1);
  const ms = next - now;
  const horas = Math.round(ms / 3600000);
  console.log(`Resumen diario programado en ${horas}hs (01:00 UTC = 22:00 Argentina)`);
  setTimeout(() => {
    sendTelegram('📊 <b>RESUMEN DIARIO AUTOMÁTICO</b>\n⏰ 22:00 hs Argentina\n\nAbrí el bot para ver tus estadísticas del día y exportar el journal.');
    setInterval(() => {
      sendTelegram('📊 <b>RESUMEN DIARIO AUTOMÁTICO</b>\n⏰ 22:00 hs Argentina\n\nAbrí el bot para ver tus estadísticas del día y exportar el journal.');
    }, 24 * 60 * 60 * 1000);
  }, ms);
}

// ── Webhook ───────────────────────────────────────────────
app.post("/webhook", async (req, res) => {
  const { secret, action, symbol, quantity, apiKey, apiSecret } = req.body;
  if (secret !== WEBHOOK_SECRET) return res.status(401).json({ error: "Secret inválido" });
  try {
    const side = action === "buy" ? "BUY" : "SELL";
    const timestamp = Date.now();
    const params = `symbol=${symbol}&side=${side}&type=MARKET&quantity=${quantity}&timestamp=${timestamp}`;
    const signature = hmac(apiSecret, params);
    const response = await fetch(`https://api.binance.com/api/v3/order`, {
      method: "POST",
      headers: { "X-MBX-APIKEY": apiKey, "Content-Type": "application/x-www-form-urlencoded" },
      body: `${params}&signature=${signature}`
    });
    const data = await response.json();
    await sendTelegram(`🎯 WEBHOOK: ${side} ${symbol}`);
    res.json({ success: true, order: data });
  } catch (e) { res.status(500).json({ error: e.message }); }
});

app.listen(PORT, () => {
  console.log(`Backend corriendo en puerto ${PORT}`);
  scheduleDailySummary();
  sendTelegram(`🟢 <b>Signal Bot Backend iniciado</b>\n⏰ ${new Date().toLocaleString('es-AR', {timeZone:'America/Argentina/Buenos_Aires'})}`);
});
