<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>加密货币实时监控</title>
<link href="https://fonts.googleapis.com/css2?family=Space+Mono:wght@400;700&family=DM+Sans:wght@300;400;500&display=swap" rel="stylesheet">
<style>
  *{box-sizing:border-box;margin:0;padding:0}
  body{font-family:'DM Sans',sans-serif;background:#0a0a0f;color:#e8e8e8;min-height:100vh}

  /* ── 密码页 ── */
  #lock-screen{
    position:fixed;inset:0;background:#0a0a0f;display:flex;flex-direction:column;
    align-items:center;justify-content:center;z-index:9999;gap:0;
  }
  .lock-logo{
    width:56px;height:56px;border-radius:16px;
    background:linear-gradient(135deg,#F7931A,#627EEA);
    display:flex;align-items:center;justify-content:center;
    font-size:26px;margin-bottom:28px;
    box-shadow:0 0 40px rgba(247,147,26,.25);
  }
  .lock-title{font-size:22px;font-weight:500;color:#fff;margin-bottom:6px;letter-spacing:-.02em}
  .lock-sub{font-size:13px;color:#666;margin-bottom:36px}
  .lock-box{
    background:#141418;border:0.5px solid #2a2a35;border-radius:16px;
    padding:28px 32px;width:100%;max-width:360px;
  }
  .lock-label{font-size:11px;font-weight:500;letter-spacing:.08em;color:#555;text-transform:uppercase;margin-bottom:8px}
  .lock-input{
    width:100%;background:#0a0a0f;border:0.5px solid #2a2a35;border-radius:10px;
    padding:12px 16px;font-size:14px;color:#e8e8e8;font-family:'DM Sans',sans-serif;
    outline:none;transition:border-color .2s;letter-spacing:.05em;
  }
  .lock-input:focus{border-color:#627EEA}
  .lock-btn{
    width:100%;margin-top:14px;padding:12px;border-radius:10px;border:none;
    background:linear-gradient(135deg,#F7931A,#e07d10);color:#fff;
    font-size:14px;font-weight:500;cursor:pointer;font-family:'DM Sans',sans-serif;
    transition:opacity .2s,transform .1s;
  }
  .lock-btn:hover{opacity:.9}
  .lock-btn:active{transform:scale(.98)}
  .lock-err{color:#ef4444;font-size:12px;margin-top:10px;text-align:center;min-height:18px;transition:opacity .3s}
  .lock-err.hidden{opacity:0}
  .shake{animation:shake .35s ease}
  @keyframes shake{0%,100%{transform:translateX(0)}25%{transform:translateX(-6px)}75%{transform:translateX(6px)}}

  /* ── 主页 ── */
  #app{display:none}
  .header{
    display:flex;align-items:center;justify-content:space-between;
    padding:14px 20px;border-bottom:0.5px solid #1e1e28;
    background:#0d0d12;position:sticky;top:0;z-index:100;
  }
  .header-left{display:flex;align-items:center;gap:10px}
  .h-dot{width:6px;height:6px;border-radius:50%;background:#22c55e;animation:pulse 1.4s ease-in-out infinite}
  @keyframes pulse{0%,100%{opacity:1}50%{opacity:.3}}
  .h-title{font-size:14px;font-weight:500;color:#e8e8e8}
  .h-clock{font-family:'Space Mono',monospace;font-size:11px;color:#555}
  .h-logout{font-size:11px;color:#444;cursor:pointer;padding:4px 10px;border:0.5px solid #2a2a35;border-radius:6px;transition:all .2s}
  .h-logout:hover{color:#e8e8e8;border-color:#555}

  .dash{display:grid;grid-template-columns:1fr 1fr;gap:10px;padding:14px}
  @media(max-width:600px){.dash{grid-template-columns:1fr}}

  .card{background:#141418;border:0.5px solid #1e1e28;border-radius:14px;padding:16px 18px}
  .card-wide{grid-column:1/-1}

  .coin-header{display:flex;align-items:center;gap:8px;margin-bottom:12px}
  .coin-dot{width:8px;height:8px;border-radius:50%}
  .coin-label{font-size:10px;font-weight:500;letter-spacing:.1em;color:#555;text-transform:uppercase}
  .price-big{font-family:'Space Mono',monospace;font-size:24px;font-weight:700;line-height:1.1;color:#fff}
  .price-change{font-size:12px;font-weight:500;margin-top:4px}
  .up{color:#22c55e}.down{color:#ef4444}
  .stat-row{display:flex;justify-content:space-between;margin-top:10px;padding-top:10px;border-top:0.5px solid #1e1e28}
  .stat-item{text-align:center}
  .stat-val{font-family:'Space Mono',monospace;font-size:11px;font-weight:700;color:#ccc}
  .stat-key{font-size:10px;color:#444;margin-top:2px}

  .section-label{font-size:10px;font-weight:500;letter-spacing:.1em;text-transform:uppercase;color:#444;margin-bottom:10px}

  .pred-grid{display:grid;grid-template-columns:repeat(4,1fr);gap:8px;margin-top:8px}
  @media(max-width:480px){.pred-grid{grid-template-columns:repeat(2,1fr)}}
  .pred-cell{background:#0d0d12;border:0.5px solid #1e1e28;border-radius:10px;padding:10px 8px;text-align:center}
  .pred-time{font-size:10px;color:#444;margin-bottom:5px}
  .pred-price{font-family:'Space Mono',monospace;font-size:11px;font-weight:700;color:#e8e8e8}
  .pred-dir{font-size:10px;margin-top:3px}

  .corr-bar-wrap{height:5px;background:#0d0d12;border-radius:3px;overflow:hidden;margin:6px 0}
  .corr-bar{height:100%;border-radius:3px;transition:width 1s ease;background:#627EEA}

  .signal-row{display:flex;gap:6px;flex-wrap:wrap;margin-top:10px}
  .signal-badge{font-size:10px;font-weight:500;padding:3px 10px;border-radius:20px;border:0.5px solid}

  .disclaimer{font-size:10px;color:#333;margin-top:12px}
</style>
</head>
<body>

<!-- ══ 密码锁页 ══ -->
<div id="lock-screen">
  <div class="lock-logo">₿</div>
  <div class="lock-title">加密货币监控</div>
  <div class="lock-sub">请输入访问密码</div>
  <div class="lock-box" id="lock-box">
    <div class="lock-label">密码</div>
    <input class="lock-input" type="password" id="pwd-input" placeholder="••••••••••" autocomplete="current-password">
    <button class="lock-btn" id="pwd-btn">进入监控面板 →</button>
    <div class="lock-err hidden" id="pwd-err">密码错误，请重试</div>
  </div>
</div>

<!-- ══ 主内容 ══ -->
<div id="app">
  <div class="header">
    <div class="header-left">
      <span class="h-dot"></span>
      <span class="h-title">实时加密货币监控</span>
    </div>
    <div style="display:flex;align-items:center;gap:12px">
      <span class="h-clock" id="clock">--:--:--</span>
      <span class="h-logout" id="logout-btn">退出</span>
    </div>
  </div>

  <div class="dash">
    <!-- BTC -->
    <div class="card">
      <div class="coin-header">
        <div class="coin-dot" style="background:#F7931A"></div>
        <span class="coin-label">Bitcoin · BTC</span>
      </div>
      <div class="price-big" id="btc-price">加载中...</div>
      <div class="price-change" id="btc-change">—</div>
      <div class="stat-row">
        <div class="stat-item"><div class="stat-val" id="btc-high">—</div><div class="stat-key">24h 高</div></div>
        <div class="stat-item"><div class="stat-val" id="btc-low">—</div><div class="stat-key">24h 低</div></div>
        <div class="stat-item"><div class="stat-val" id="btc-vol">—</div><div class="stat-key">成交量</div></div>
      </div>
    </div>

    <!-- ETH -->
    <div class="card">
      <div class="coin-header">
        <div class="coin-dot" style="background:#627EEA"></div>
        <span class="coin-label">Ethereum · ETH</span>
      </div>
      <div class="price-big" id="eth-price">加载中...</div>
      <div class="price-change" id="eth-change">—</div>
      <div class="stat-row">
        <div class="stat-item"><div class="stat-val" id="eth-high">—</div><div class="stat-key">24h 高</div></div>
        <div class="stat-item"><div class="stat-val" id="eth-low">—</div><div class="stat-key">24h 低</div></div>
        <div class="stat-item"><div class="stat-val" id="eth-vol">—</div><div class="stat-key">成交量</div></div>
      </div>
    </div>

    <!-- 走势图 -->
    <div class="card card-wide">
      <div class="section-label">价格走势（最近 30 次刷新）</div>
      <div style="position:relative;width:100%;height:140px">
        <canvas id="priceChart" role="img" aria-label="BTC 与 ETH 近期价格走势折线图">BTC/ETH 价格走势图</canvas>
      </div>
    </div>

    <!-- 预测 -->
    <div class="card card-wide">
      <div class="section-label">BTC 未来走势预测（基于 ETH 相关性）</div>
      <div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:4px">
        <span style="font-size:11px;color:#555">ETH → BTC 相关系数</span>
        <span style="font-family:'Space Mono',monospace;font-size:11px;font-weight:700;color:#627EEA" id="corr-val">计算中...</span>
      </div>
      <div class="corr-bar-wrap"><div class="corr-bar" id="corr-bar" style="width:0%"></div></div>
      <div class="pred-grid" id="pred-grid">
        <div class="pred-cell"><div class="pred-time">1 小时后</div><div class="pred-price" id="p1">—</div><div class="pred-dir" id="d1">—</div></div>
        <div class="pred-cell"><div class="pred-time">2 小时后</div><div class="pred-price" id="p2">—</div><div class="pred-dir" id="d2">—</div></div>
        <div class="pred-cell"><div class="pred-time">4 小时后</div><div class="pred-price" id="p3">—</div><div class="pred-dir" id="d3">—</div></div>
        <div class="pred-cell"><div class="pred-time">8 小时后</div><div class="pred-price" id="p4">—</div><div class="pred-dir" id="d4">—</div></div>
      </div>
      <div class="signal-row" id="signal-row"></div>
      <div class="disclaimer">⚠ 预测基于统计相关性模型，仅供参考，不构成投资建议。</div>
    </div>
  </div>
</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.js"></script>
<script>
// ── 密码逻辑 ──
const PASS = 'qwertyuiop';
const SESSION_KEY = 'crypto_auth';

function checkSession(){
  return sessionStorage.getItem(SESSION_KEY) === '1';
}
function unlock(){
  sessionStorage.setItem(SESSION_KEY,'1');
  document.getElementById('lock-screen').style.display='none';
  document.getElementById('app').style.display='block';
  initApp();
}

if(checkSession()) unlock();

document.getElementById('pwd-btn').addEventListener('click', tryLogin);
document.getElementById('pwd-input').addEventListener('keydown', e=>{ if(e.key==='Enter') tryLogin(); });

function tryLogin(){
  const val = document.getElementById('pwd-input').value;
  const errEl = document.getElementById('pwd-err');
  if(val === PASS){
    unlock();
  } else {
    errEl.classList.remove('hidden');
    const box = document.getElementById('lock-box');
    box.classList.remove('shake');
    void box.offsetWidth;
    box.classList.add('shake');
    document.getElementById('pwd-input').value='';
    setTimeout(()=>errEl.classList.add('hidden'), 2500);
  }
}

document.getElementById('logout-btn').addEventListener('click',()=>{
  sessionStorage.removeItem(SESSION_KEY);
  location.reload();
});

// ── 主应用逻辑 ──
let btcHist=[], ethHist=[], labels=[], chart=null;

const fmt = v=>'$'+Number(v).toLocaleString('en-US',{minimumFractionDigits:0,maximumFractionDigits:0});
const fmtM = v=>{ if(v>=1e9)return(v/1e9).toFixed(1)+'B'; if(v>=1e6)return(v/1e6).toFixed(0)+'M'; return v.toFixed(0); };

function initApp(){
  initChart();
  fetchPrices();
  setInterval(fetchPrices, 30000);
  setInterval(tick, 1000);
  tick();
}

function initChart(){
  const ctx = document.getElementById('priceChart').getContext('2d');
  chart = new Chart(ctx,{
    type:'line',
    data:{
      labels:[],
      datasets:[
        {label:'BTC',data:[],borderColor:'#F7931A',backgroundColor:'rgba(247,147,26,.07)',borderWidth:1.5,pointRadius:0,tension:.4,yAxisID:'yBTC',fill:true},
        {label:'ETH',data:[],borderColor:'#627EEA',backgroundColor:'rgba(98,126,234,.07)',borderWidth:1.5,pointRadius:0,tension:.4,yAxisID:'yETH',fill:true}
      ]
    },
    options:{
      responsive:true,maintainAspectRatio:false,animation:false,
      plugins:{
        legend:{display:false},
        tooltip:{mode:'index',intersect:false,backgroundColor:'#141418',borderColor:'#2a2a35',borderWidth:1,titleColor:'#888',bodyColor:'#e8e8e8',
          callbacks:{label:i=>i.dataset.label+': $'+Number(i.raw).toLocaleString()}}
      },
      scales:{
        x:{ticks:{color:'#444',font:{size:9},maxTicksLimit:6,maxRotation:0},grid:{color:'rgba(255,255,255,.03)'},border:{display:false}},
        yBTC:{position:'left',ticks:{color:'#F7931A',font:{size:9,family:'Space Mono'},callback:v=>'$'+Number(v).toLocaleString(),maxTicksLimit:5},grid:{color:'rgba(255,255,255,.04)'},border:{display:false}},
        yETH:{position:'right',ticks:{color:'#627EEA',font:{size:9,family:'Space Mono'},callback:v=>'$'+Number(v).toLocaleString(),maxTicksLimit:5},grid:{display:false},border:{display:false}}
      }
    }
  });
}

function pearson(a,b){
  if(a.length<2)return 0;
  const n=a.length,ma=a.reduce((s,x)=>s+x,0)/n,mb=b.reduce((s,x)=>s+x,0)/n;
  let num=0,da=0,db=0;
  for(let i=0;i<n;i++){num+=(a[i]-ma)*(b[i]-mb);da+=(a[i]-ma)**2;db+=(b[i]-mb)**2;}
  return(da&&db)?num/Math.sqrt(da*db):0;
}

function updatePredictions(btcPrice,btcChange24h,ethChange24h){
  if(btcHist.length<3)return;
  const corr=pearson(ethHist.slice(-20),btcHist.slice(-20));
  const ethMom=ethHist.length>=2?(ethHist[ethHist.length-1]-ethHist[ethHist.length-2])/ethHist[ethHist.length-2]:0;
  const trendFactor=ethChange24h/100;

  const preds=[1,2,4,8].map((h,i)=>{
    const decay=Math.pow(0.72,i);
    const drift=trendFactor*corr*decay*h*0.18;
    const momEffect=ethMom*corr*decay*0.8;
    return btcPrice+(drift+momEffect)*btcPrice;
  });

  const corrPct=Math.round(Math.abs(corr)*100);
  document.getElementById('corr-val').textContent=corr.toFixed(3);
  document.getElementById('corr-bar').style.width=corrPct+'%';

  [['p1','d1'],['p2','d2'],['p3','d3'],['p4','d4']].forEach(([pid,did],i)=>{
    const diff=preds[i]-btcPrice, pct=(diff/btcPrice*100).toFixed(2);
    document.getElementById(pid).textContent=fmt(preds[i]);
    const el=document.getElementById(did);
    if(diff>=0){el.textContent='▲ +'+pct+'%';el.className='pred-dir up';}
    else{el.textContent='▼ '+pct+'%';el.className='pred-dir down';}
  });

  const overallDir=preds[3]>btcPrice;
  const signals=[];
  if(Math.abs(corr)>0.7)signals.push({t:'高相关性',c:'rgba(98,126,234,.15)',b:'#627EEA'});
  if(ethChange24h>2)signals.push({t:'ETH 强势↑',c:'rgba(34,197,94,.15)',b:'#22c55e'});
  else if(ethChange24h<-2)signals.push({t:'ETH 弱势↓',c:'rgba(239,68,68,.15)',b:'#ef4444'});
  signals.push({t:overallDir?'BTC 看涨':'BTC 看跌',c:overallDir?'rgba(34,197,94,.15)':'rgba(239,68,68,.15)',b:overallDir?'#22c55e':'#ef4444'});
  document.getElementById('signal-row').innerHTML=signals.map(s=>`<span class="signal-badge" style="background:${s.c};border-color:${s.b};color:${s.b}">${s.t}</span>`).join('');
}

async function fetchPrices(){
  try{
    const r=await fetch('https://api.coingecko.com/api/v3/simple/price?ids=bitcoin,ethereum&vs_currencies=usd&include_24hr_change=true&include_24hr_vol=true&include_high_24h=true&include_low_24h=true');
    const d=await r.json();
    const btc=d.bitcoin,eth=d.ethereum;
    const bp=btc.usd,ep=eth.usd,bc=btc.usd_24h_change||0,ec=eth.usd_24h_change||0;

    document.getElementById('btc-price').textContent=fmt(bp);
    const bce=document.getElementById('btc-change');
    bce.textContent=(bc>=0?'▲ +':'▼ ')+bc.toFixed(2)+'%（24h）';
    bce.className='price-change '+(bc>=0?'up':'down');
    document.getElementById('btc-high').textContent=fmt(btc.usd_24h_high);
    document.getElementById('btc-low').textContent=fmt(btc.usd_24h_low);
    document.getElementById('btc-vol').textContent=fmtM(btc.usd_24h_vol||0);

    document.getElementById('eth-price').textContent=fmt(ep);
    const ece=document.getElementById('eth-change');
    ece.textContent=(ec>=0?'▲ +':'▼ ')+ec.toFixed(2)+'%（24h）';
    ece.className='price-change '+(ec>=0?'up':'down');
    document.getElementById('eth-high').textContent=fmt(eth.usd_24h_high);
    document.getElementById('eth-low').textContent=fmt(eth.usd_24h_low);
    document.getElementById('eth-vol').textContent=fmtM(eth.usd_24h_vol||0);

    const now=new Date();
    const lbl=now.getHours().toString().padStart(2,'0')+':'+now.getMinutes().toString().padStart(2,'0');
    btcHist.push(bp);ethHist.push(ep);labels.push(lbl);
    if(btcHist.length>30){btcHist.shift();ethHist.shift();labels.shift();}

    if(chart){
      chart.data.labels=[...labels];
      chart.data.datasets[0].data=[...btcHist];
      chart.data.datasets[1].data=[...ethHist];
      chart.update('none');
    }
    updatePredictions(bp,bc,ec);
  }catch(e){console.error(e);}
}

function tick(){
  const now=new Date();
  document.getElementById('clock').textContent=now.toLocaleTimeString('zh-CN');
}
</script>
</body>
</html>
