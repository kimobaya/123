# 123
chart pro
<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="utf-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1"/>
<title>eToroPro • News & Charts</title>
<meta name="description" content="News-basiertes Daytrading-Dashboard mit Trend-Discovery, dynamischer Watchlist, Charts und Live-Marktdaten."/>
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600&display=swap" rel="stylesheet">
<style>
:root{
  --bg:#0b0f14;--surface:#0f151e;--card:#121a24;--muted:#9fb0c3;--text:#e6edf3;--link:#7fb6ff;
  --border:#223140;--accent:#2ea043;--danger:#ff6b6b;
}
*{box-sizing:border-box}
html,body{margin:0;background:var(--bg);color:var(--text);font-family:Inter,system-ui,Segoe UI,Helvetica,Arial,sans-serif}
a{color:var(--link);text-decoration:none} a:hover{text-decoration:underline}
header{padding:18px;border-bottom:1px solid var(--border);position:sticky;top:0;z-index:10;
  background:linear-gradient(180deg,rgba(11,15,20,.95),rgba(11,15,20,.75))}
h1{margin:0;font-size:22px;letter-spacing:.2px}
.sub{color:var(--muted);font-size:12px;margin-top:4px}
main{max-width:1250px;margin:0 auto;padding:16px;display:grid;gap:16px}
.grid{display:grid;gap:16px}
@media(min-width:1100px){.grid{grid-template-columns:1.25fr .75fr}}
.card{background:var(--card);border:1px solid var(--border);border-radius:14px;padding:16px}
.card h2{margin:0 0 10px;font-size:16px}
.row{display:flex;gap:8px;flex-wrap:wrap;align-items:center}
.btn{background:#0f2532;border:1px solid var(--border);color:var(--text);padding:10px 14px;border-radius:10px;cursor:pointer;font-weight:600;font-size:14px}
.btn:hover{filter:brightness(1.12)}
input{background:#0e141b;border:1px solid var(--border);border-radius:10px;color:var(--text);padding:10px 12px}
.small{font-size:12px;color:var(--muted)}
.list{display:grid;gap:10px}
.item{padding:12px;border:1px dashed var(--border);border-radius:12px;background:#0e141b}
.item h3{margin:0 0 8px;font-size:14px}
.badge{font-size:11px;padding:2px 8px;border-radius:6px;border:1px solid var(--border);background:#0e1822;color:var(--muted)}
.badge.green{color:var(--accent);border-color:#1e4728;background:#0d1a12}
.badge.red{color:var(--danger);border-color:#4b1f1f;background:#190d0d}
.sep{height:1px;background:var(--border);margin:14px 0}
footer{max-width:1250px;margin:22px auto 40px;padding:0 16px;color:var(--muted)}
.kpis{display:grid;gap:10px;grid-template-columns:repeat(2,1fr)}
@media(min-width:680px){.kpis{grid-template-columns:repeat(4,1fr)}}
.kpi{background:var(--surface);border:1px solid var(--border);border-radius:12px;padding:12px}
.kpi .v{font-weight:700;font-size:18px}
.chartWrap{background:var(--surface);border:1px solid var(--border);border-radius:12px;padding:12px}
.hint{margin-top:6px}
</style>
</head>
<body>
<header>
  <h1>eToroPro • News & Charts</h1>
  <div class="sub"><span id="today"></span> · Auto-Update beim Laden · Zeitzone: Europe/Berlin</div>
</header>

<main>
  <!-- KPIs -->
  <section class="kpis">
    <div class="kpi"><div class="small">Gesamt-Mentions</div><div id="kpiMentions" class="v">–</div></div>
    <div class="kpi"><div class="small">Ø-Sentiment</div><div id="kpiSent" class="v">–</div></div>
    <div class="kpi"><div class="small">Watchlist</div><div id="kpiWL" class="v">–</div></div>
    <div class="kpi"><div class="small">Letztes Update</div><div id="kpiUpd" class="v">–</div></div>
  </section>

  <section class="grid">
    <!-- LEFT: Discovery + Watchlist -->
    <section class="card">
      <h2>🔥 Trend-Discovery & Watchlist</h2>
      <div class="row">
        <input id="addTicker" placeholder="Ticker hinzufügen (z. B. NVDA, TSLA, AAPL)"/>
        <button class="btn" id="addBtn">Hinzufügen</button>
        <button class="btn" id="scanBtn">Jetzt scannen</button>
        <button class="btn" id="clearBtn">Liste leeren</button>
        <span class="badge">Lookback: <b id="lookbackPill"></b></span>
      </div>
      <div class="sep"></div>
      <div id="watchlist" class="list"></div>
      <p class="small hint">Bias = Summe der +1/0/−1-Schlagwörter aus frischen Google-News-Headlines. Entry: 5-Min-ORB & VWAP (mit Volumen). Kein Overnight.</p>
    </section>

    <!-- RIGHT: Charts -->
    <aside class="card">
      <h2>📊 Visuals</h2>
      <div class="chartWrap"><canvas id="barMentions" height="180" aria-label="Mentions Chart"></canvas></div>
      <div class="chartWrap" style="margin-top:12px"><canvas id="barSentiment" height="180" aria-label="Sentiment Chart"></canvas></div>
      <div class="chartWrap" style="margin-top:12px"><canvas id="pieBias" height="180" aria-label="Bias Chart"></canvas></div>
    </aside>
  </section>

  <!-- LIVE MARKETS -->
  <section class="card">
    <h2>📈 Live-Märkte (TradingView)</h2>
    <div class="tradingview-widget-container"><div id="tv_overview"></div></div>
    <p class="small hint">Klicke in der Watchlist auf „TradingView“, um direkt in den Einzelchart zu springen.</p>
  </section>

  <!-- HOTLISTS -->
  <section class="card">
    <h2>🔥 Hotlists / Most Active</h2>
    <div class="tradingview-widget-container"><div id="tv_hotlists"></div></div>
  </section>

  <!-- HEADLINES -->
  <section class="card">
    <h2>📰 Frische Schlagzeilen</h2>
    <div id="headlines" class="list"></div>
  </section>
</main>

<footer>
  🚨 <strong>Risikohinweis:</strong> Daytrading ist hochriskant. Dies ist keine Anlageberatung. Teste das Regelwerk zunächst im Demokonto.
</footer>

<!-- Chart.js -->
<script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.1/dist/chart.umd.min.js" integrity="sha384-p5JzQx2r0T0f6IL7d2kB5kZk7J9c9rjJ7gk3Zg1h8XcJ9h9cGmZp1d2o"/>

<script>
// ===== CONFIG =====
const CONFIG = {
  timezone:"Europe/Berlin",
  lookbackHours: 30,
  maxPerTicker: 6,
  globalQueries: ["earnings","guidance","upgrade","downgrade","acquisition","merger","stake","ETF inflows","most active"],
  defaultTickers: ["NVDA","AAPL","MSFT","TSLA","AMD","META","GOOGL","AMZN"],
  pos:["beats","beat","tops","raises guidance","raises outlook","surge","soars","approval","approved","upgrade","overweight","buy rating","inflows","accumulates","record","strong","bullish"],
  neg:["misses","cuts guidance","cuts outlook","downgrade","underweight","sell rating","investigation","probe","lawsuit","recall","slumps","plunge","falls","warning","outflows","halt"]
};

// ===== Helpers =====
const $ = s => document.querySelector(s);
const fmt = d => new Intl.DateTimeFormat('de-DE',{weekday:'short',year:'numeric',month:'2-digit',day:'2-digit',hour:'2-digit',minute:'2-digit',timeZone:CONFIG.timezone}).format(d);
const within = t => (Date.now()-new Date(t).getTime())<=CONFIG.lookbackHours*3600*1000;
const score = txt => { const t=(txt||"").toLowerCase(); let s=0; CONFIG.pos.forEach(k=>t.includes(k)&&s++); CONFIG.neg.forEach(k=>t.includes(k)&&s--); return s; };
const rssUrl = q => "https://api.rss2json.com/v1/api.json?rss_url="+encodeURIComponent(`https://news.google.com/rss/search?q=${encodeURIComponent(q+" stock OR shares")}&hl=en-US&gl=US&ceid=US:en`);
async function fetchNews(q){try{const r=await fetch(rssUrl(q)); if(!r.ok) throw new Error(r.status); const j=await r.json(); return (j.items||[]).filter(i=>i?.title && within(i.pubDate));}catch(e){return[]}}
function exGuess(t){return ["AAPL","MSFT","AMZN","GOOGL","META","NVDA","TSLA","AMD","MU","PLTR","OKTA","MDB","CRWD","SNOW","ZS","ABNB","UBER","RIVN","LCID","NKE","KSS"].includes(t)?"NASDAQ":"NYSE"}

// ===== State =====
let tickers = [...CONFIG.defaultTickers];
let dataMap = new Map(); // ticker -> {sum, mentions, headlines[]}

// ===== Charts =====
let chartMentions, chartSentiment, chartBias;
function makeBar(ctx, labels, values, title){
  return new Chart(ctx,{type:"bar",data:{labels,datasets:[{label:title,data:values}]},
    options:{responsive:true,plugins:{legend:{display:false}},scales:{x:{grid:{display:false}},y:{grid:{color:"#1c2a39"}}}}});
}
function makePie(ctx, labels, values, title){
  return new Chart(ctx,{type:"doughnut",data:{labels,datasets:[{label:title,data:values}]} ,
    options:{responsive:true,plugins:{legend:{position:"bottom"}}}});
}
function updateCharts(){
  const items = [...dataMap.entries()].map(([t,v])=>({t,sum:v.sum,mentions:v.mentions}));
  items.sort((a,b)=>b.mentions-a.mentions);
  const top = items.slice(0,10);
  const labels = top.map(x=>x.t);
  const vMentions = top.map(x=>x.mentions);
  const vSent = top.map(x=>x.sum);

  if(chartMentions) chartMentions.destroy();
  chartMentions = makeBar($("#barMentions").getContext("2d"), labels, vMentions, "Mentions");

  if(chartSentiment) chartSentiment.destroy();
  chartSentiment = makeBar($("#barSentiment").getContext("2d"), labels, vSent, "Sentiment-Summe");

  const biasCounts = {Long:0,Neutral:0,Short:0};
  items.forEach(x=>{ if(x.sum>=2) biasCounts.Long++; else if(x.sum<=-2) biasCounts.Short++; else biasCounts.Neutral++; });
  if(chartBias) chartBias.destroy();
  chartBias = makePie($("#pieBias").getContext("2d"), ["Long","Neutral","Short"], [biasCounts.Long,biasCounts.Neutral,biasCounts.Short], "Bias");
}

// ===== UI =====
function renderHeader(){
  $("#today").textContent = "Heute: "+fmt(new Date());
  $("#lookbackPill").textContent = CONFIG.lookbackHours+"h";
}
function renderKPIs(){
  const arr=[...dataMap.values()];
  const m = arr.reduce((a,v)=>a+v.mentions,0);
  const sAvg = arr.length? (arr.reduce((a,v)=>a+v.sum,0)/arr.length).toFixed(2) : "–";
  $("#kpiMentions").textContent = m||"–";
  $("#kpiSent").textContent = sAvg;
  $("#kpiWL").textContent = tickers.length;
  $("#kpiUpd").textContent = fmt(new Date());
}
function cardHTML(t, obj){
  const badge = obj.sum>=2? `<span class="badge green">Bias: Long · +${obj.sum}</span>` :
               obj.sum<=-2? `<span class="badge red">Bias: Short · ${obj.sum}</span>` :
               `<span class="badge">Bias: Neutral · ${obj.sum>=0?"+":""}${obj.sum}</span>`;
  const tv = `https://www.tradingview.com/chart/?symbol=${encodeURIComponent(exGuess(t)+":"+t)}`;
  const etoro = `https://www.etoro.com/markets/${t.toLowerCase()}`;
  let html = `<div class="item"><h3>${t} ${badge} <span class="badge">Mentions: ${obj.mentions}</span></h3>
    <div class="row"><a class="btn" href="${tv}" target="_blank" rel="noopener">TradingView</a>
    <a class="btn" href="${etoro}" target="_blank" rel="noopener">eToro</a>
    <button class="btn" data-del="${t}">Entfernen</button></div><div class="sep"></div>`;
  if(obj.headlines.length===0){
    html += `<div class="small">Keine frischen Headlines im Lookback-Fenster.</div>`;
  }else{
    obj.headlines.slice(0,CONFIG.maxPerTicker).forEach(h=>{
      const tag = h.s>0?'<span class="badge green">+1</span>':(h.s<0?'<span class="badge red">−1</span>':'<span class="badge">0</span>');
      html += `<div class="small">${tag} <a href="${h.link}" target="_blank" rel="noopener">${h.title}</a>
               <div class="small" style="color:var(--muted)">${fmt(new Date(h.pubDate))}</div></div>`;
    });
  }
  html += `</div>`;
  return html;
}
function bindCardActions(){
  document.querySelectorAll('[data-del]').forEach(btn=>{
    btn.addEventListener('click',()=>{
      const t = btn.getAttribute('data-del');
      tickers = tickers.filter(x=>x!==t);
      dataMap.delete(t);
      drawWatchlist(); renderKPIs(); updateCharts();
    });
  });
}
function drawWatchlist(){
  const box = $("#watchlist");
  if(dataMap.size===0){ box.innerHTML = `<div class="small">Noch nichts gescannt. Klicke <b>Jetzt scannen</b> oder füge Ticker oben hinzu.</div>`; return; }
  const items = [...dataMap.entries()].sort((a,b)=>{
    const A=a[1], B=b[1];
    const s = Math.abs(B.sum)-Math.abs(A.sum);
    if(s!==0) return s;
    const ta = new Date(A.headlines[0]?.pubDate||0); const tb = new Date(B.headlines[0]?.pubDate||0);
    return tb - ta;
  });
  box.innerHTML = items.map(([t,v])=>cardHTML(t,v)).join("");
  bindCardActions();
}

// ===== Data flow =====
async function scanTicker(t){
  const rows = await fetchNews(t);
  rows.sort((a,b)=>new Date(b.pubDate)-new Date(a.pubDate));
  const enriched = rows.slice(0,CONFIG.maxPerTicker).map(h=>({title:h.title,link:h.link,pubDate:h.pubDate,s:score(h.title)}));
  const sum = enriched.reduce((a,h)=>a+h.s,0);
  dataMap.set(t,{sum,mentions:rows.length,headlines:enriched});
}
async function scanAll(){
  dataMap.clear();
  for(const t of tickers){ await scanTicker(t); }
  drawWatchlist(); renderKPIs(); updateCharts();
}
async function buildHeadlines(){
  const box=$("#headlines"); box.innerHTML = `<div class="small">Lade Headlines…</div>`;
  let pool=[]; for(const q of CONFIG.globalQueries){ const arr = await fetchNews(q); pool = pool.concat(arr); }
  pool.sort((a,b)=>new Date(b.pubDate)-new Date(a.pubDate));
  box.innerHTML = pool.slice(0,24).map(h=>{
    const s = score(h.title);
    const tag = s>0?'<span class="badge green">+1</span>':(s<0?'<span class="badge red">−1</span>':'<span class="badge">0</span>');
    return `<div class="item"><div>${tag} <a href="${h.link}" target="_blank" rel="noopener">${h.title}</a></div>
            <div class="small" style="color:var(--muted)">${fmt(new Date(h.pubDate))}</div></div>`;
  }).join("");
}

// ===== Events =====
$("#addBtn").addEventListener("click",()=>{
  const v = $("#addTicker").value.toUpperCase().trim();
  if(v && !tickers.includes(v)) tickers.push(v);
  $("#addTicker").value=""; scanAll();
});
$("#scanBtn").addEventListener("click",()=>scanAll());
$("#clearBtn").addEventListener("click",()=>{ tickers=[]; dataMap.clear(); drawWatchlist(); renderKPIs(); updateCharts(); });

// ===== Boot =====
(async function boot(){
  renderHeader();
  await scanAll();
  await buildHeadlines();
})();
</script>

<!-- TradingView Widgets -->
<script>
function mountTV(){
  // Symbol Overview
  const syms = (["NVDA","AAPL","MSFT","TSLA","AMD","META","GOOGL","AMZN"]).map(t=>{
    return (["AAPL","MSFT","AMZN","GOOGL","META","NVDA","TSLA","AMD"].includes(t)?"NASDAQ":"NYSE")+":"+t;
  });
  const s1=document.createElement("script");
  s1.type="text/javascript"; s1.src="https://s3.tradingview.com/external-embedding/embed-widget-symbol-overview.js"; s1.async=true;
  s1.innerHTML = JSON.stringify({symbols: syms.map(x=>[x]), colorTheme:"dark", autosize:true, width:"100%", height:420, showVolume:true, showMA:true, locale:"en"});
  document.querySelector("#tv_overview").innerHTML=""; document.querySelector("#tv_overview").appendChild(s1);

  // Hotlists
  const s2=document.createElement("script");
  s2.type="text/javascript"; s2.src="https://s3.tradingview.com/external-embedding/embed-widget-hotlists.js"; s2.async=true;
  s2.innerHTML = JSON.stringify({exchange:"US", colorTheme:"dark", width:"100%", height:420, locale:"en"});
  document.querySelector("#tv_hotlists").innerHTML=""; document.querySelector("#tv_hotlists").appendChild(s2);
}
document.addEventListener("DOMContentLoaded", mountTV);
</script>
</body>
</html>
