<!doctype html>
<html lang="ja">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no,viewport-fit=cover">
<title>COIN DROP</title>
<style>
*{box-sizing:border-box;-webkit-tap-highlight-color:transparent}
html,body{margin:0;width:100%;height:100%;overflow-x:hidden;background:#080b13;color:#fff;font-family:Arial,sans-serif}
button,select{font:inherit}
button{border:1px solid #52678d;border-radius:7px;padding:7px;background:linear-gradient(#293957,#18233a);color:white;font-weight:800;font-size:11px}
button:disabled{opacity:.35}
.app{max-width:1100px;margin:auto;padding:8px;display:flex;flex-direction:column;gap:8px;min-height:100vh}
header{height:38px;flex-shrink:0;display:flex;align-items:center;justify-content:space-between}
.logo{font-weight:1000;font-size:21px;font-style:italic;color:#ffd45c}
.small{font-size:9px;color:#aab9d3}
.money{font-weight:900;font-size:18px;color:#ffd45c}
.pill{padding:4px 8px;border:1px solid #303e5d;border-radius:7px;background:#121b2c}
.layout{display:grid;grid-template-columns:1fr;gap:8px}
@media(min-width:768px){
 .layout{grid-template-columns:1.1fr .9fr}
}
.panel{min-width:0;min-height:0;overflow:hidden;padding:8px;border:1px solid #303e5d;border-radius:9px;background:linear-gradient(145deg,#141d30,#0d1320)}
.game{display:flex;flex-direction:column;gap:6px}
.stats{display:grid;grid-template-columns:1fr 1fr;gap:4px;flex-shrink:0}
.stat{padding:5px;background:#0a1020;border-radius:6px}
.stat b{font-size:13px}
.progress{height:4px;background:#26334c;border-radius:5px;overflow:hidden;margin-top:3px}
.progress div{height:100%;background:linear-gradient(90deg,#48e8ad,#ffdf68)}
canvas{display:block;width:100%;height:auto;aspect-ratio:600/580;background:#0b1222;border-radius:6px}
.zones{display:grid;grid-template-columns:repeat(5,1fr);gap:2px;flex-shrink:0}
.zone{text-align:center;padding:5px 0;background:#17233a;border:1px solid #384b6b;border-radius:4px;font-weight:900;font-size:11px;color:#ffd45c}
.zone.bad{color:#ff7187;background:#361923}
.notice{height:16px;flex-shrink:0;text-align:center;font-size:10px;color:#4dffb5;white-space:nowrap}
.primary{background:linear-gradient(135deg,#ffe78a,#ffad27);color:#251700;border:0;font-size:13px;padding:10px}
.full{width:100%;flex-shrink:0}
.toggle{display:flex;align-items:center;gap:5px;padding:6px;background:#0a1020;border-radius:6px;font-size:10px;flex-shrink:0}
.toggle input{margin:0}
.right{display:grid;grid-template-rows:auto auto auto minmax(0,1fr);gap:6px;min-height:0}
.title{font-size:12px;font-weight:900;color:#ffd45c;margin-bottom:5px}
.controls{display:grid;grid-template-columns:1fr 1fr;gap:4px}
.controls label{display:block;font-size:9px;color:#aab9d3;margin-bottom:3px}
select{width:100%;background:#080f1c;color:white;border:1px solid #465b7d;border-radius:5px;padding:6px 3px;font-size:11px}
.upgrade{display:flex;align-items:center;justify-content:space-between;gap:4px;padding:6px;margin-top:4px;border:1px solid #293b59;border-radius:6px;background:#0a1020}
.upgrade b{font-size:10px}
.upgrade p{font-size:8px;color:#aab9d3;margin:2px 0}
.upgrade button{min-width:76px;font-size:9px;padding:6px 3px}
#rebirth{background:linear-gradient(135deg,#30205d,#17172d);border-color:#8c72cf}
.purple{color:#c5aaff}
#log{font-size:9px;line-height:1.4;color:#b4c2da;overflow:hidden;max-height:80px}
</style>
</head>
<body>
<div class="app">
<header>
 <div><div class="logo">🪙 COIN DROP</div><div class="small">DROP · WIN · REBIRTH</div></div>
 <div class="pill"><div class="small">所持コイン</div><div class="money" id="money">10,000</div></div>
</header>
<div class="layout">
<section class="panel game">
 <div class="stats">
  <div class="stat"><div class="small">ユーザーレベル</div><b id="level">Lv.1</b><div class="progress"><div id="levelbar"></div></div><div class="small" id="levelprog">0 / 10</div></div>
  <div class="stat"><div class="small">転生回数</div><b class="purple" id="rebirthCount">0回</b><div class="small">獲得倍率 <span id="bonus">×1.00</span></div></div>
 </div>
 <canvas id="board" width="600" height="580"></canvas>
 <div class="zones"><div class="zone">×10</div><div class="zone bad">−20%</div><div class="zone">×2</div><div class="zone">×1.5</div><div class="zone">×5</div></div>
 <div class="notice" id="notice">コインをドロップ！</div>
 <button class="primary full" id="drop">🪙 コインをドロップ</button>
 <label class="toggle"><input type="checkbox" id="continuous"><span id="continuousLabel">🔒 Lv.7で連続ドロップ解放</span></label>
</section>
<div class="right">
 <section class="panel">
  <div class="title">🎮 ドロップ設定</div>
  <div class="controls">
   <div><label>掛け金</label><select id="bet"><option value="100">100</option><option value="500">500</option><option value="1000">1,000</option><option value="5000">5,000</option><option value="10000">10,000</option><option value="50000">50,000</option><option value="100000">100,000</option><option value="500000">500,000</option><option value="1000000">1,000,000</option><option value="5000000">5,000,000</option></select></div>
   <div><label>掛け金上限</label><div class="pill" id="cap">100</div></div>
   <button id="maxbet">MAX BET</button><button id="save">セーブ</button>
  </div>
 </section>
 <section class="panel">
  <div class="title">🔒 強化ショップ <span class="small" id="shopStatus">Lv.7で解放</span></div>
  <div class="upgrade"><div><b>🍀 当たりやすさ強化</b><p>マイナスゾーンを避けやすくする</p><p>Lv.<span id="luckLevel">0</span></p></div><button id="luckBuy">🔒 Lv.7</button></div>
  <div class="upgrade"><div><b>⚡ 倍率レベルアップ</b><p>獲得倍率アップ</p><p>Lv.<span id="multiLevel">0</span></p></div><button id="multiBuy">🔒 Lv.7</button></div>
  <div class="small">価格は購入ごとに1.5倍。倍率強化は2倍価格。</div>
 </section>
 <section class="panel" id="rebirth">
  <div class="title purple">♻ 転生</div>
  <div class="stats">
   <div class="stat"><div class="small">必要所持金</div><b id="need">1,000,000</b></div>
   <div class="stat"><div class="small">転生後倍率</div><b class="purple" id="nextBonus">×1.05</b></div>
  </div>
  <div class="progress"><div id="rebirthBar"></div></div>
  <div class="small" id="rebirthProgress" style="margin:5px 0">あと990,000コイン</div>
  <button class="full" id="rebirthBtn" disabled>🔒 所持金不足</button>
  <div class="small" style="margin-top:5px">所持金・レベル・強化がリセット。転生回数とボーナスは維持。</div>
  <div class="title" style="margin-top:8px">📜 履歴</div><div id="log">ゲーム開始！</div>
  <button id="reset" style="width:100%;margin-top:5px">データ完全リセット</button>
 </section>
</div>
</div>
</div>
<script>
(()=>{
"use strict";
const $=id=>document.getElementById(id);
const KEY="COIN_DROP_PORTRAIT_V1";
const levels=[0,10,25,45,70,100,140,190,250,320];
const caps=[100,500,1000,5000,10000,50000,100000,500000,1000000,5000000];
const initial={money:10000,plays:0,rebirths:0,luck:0,multi:0};
let s={...initial};
try{s={...s,...JSON.parse(localStorage.getItem(KEY)||"{}")}}catch(e){}
let busy=false,holding=false;
const canvas=$("board"),ctx=canvas.getContext("2d");
const fmt=n=>Math.floor(n).toLocaleString("en-US");
const lv=()=>{let n=1;for(let i=0;i<levels.length;i++)if(s.plays>=levels[i])n=i+1;return n};
const cap=()=>caps[lv()-1]||5000000;
const need=()=>1000000*Math.pow(5,s.rebirths);
const rb=()=>1+s.rebirths*.05;
const luckPrice=()=>Math.floor(10000*Math.pow(1.5,s.luck));
const multiPrice=()=>luckPrice()*2;
const unlocked=()=>lv()>=7;
function save(){try{localStorage.setItem(KEY,JSON.stringify(s))}catch(e){}}
function log(t){$("log").innerHTML=t+"<br>"+$("log").innerHTML;const a=$("log").innerHTML.split("<br>");$("log").innerHTML=a.slice(0,4).join("<br>")}
function say(t){$("notice").textContent=t}
function update(){
 const level=lv(),prev=levels[level-1],next=levels[level]||null;
 $("money").textContent=fmt(s.money);$("level").textContent="Lv."+level;
 $("levelprog").textContent=next?`${s.plays-prev} / ${next-prev} プレイ`:`MAX · ${s.plays}`;
 $("levelbar").style.width=(next?100*(s.plays-prev)/(next-prev):100)+"%";
 $("cap").textContent=fmt(cap());$("rebirthCount").textContent=s.rebirths+"回";
 $("bonus").textContent="×"+rb().toFixed(2);$("nextBonus").textContent="×"+(rb()+.05).toFixed(2);
 $("need").textContent=fmt(need());$("rebirthBar").style.width=Math.min(100,s.money/need()*100)+"%";
 $("rebirthProgress").textContent=s.money>=need()?"転生可能！":`あと${fmt(need()-s.money)}コイン`;
 $("rebirthBtn").disabled=s.money<need()||busy;
 $("rebirthBtn").textContent=s.money>=need()?"♻ 転生する":"🔒 所持金不足";
 $("shopStatus").textContent=unlocked()?"解放済み":"Lv.7で解放";
 $("luckLevel").textContent=s.luck;$("multiLevel").textContent=s.multi;
 $("luckBuy").disabled=!unlocked()||busy||s.money<luckPrice();
 $("multiBuy").disabled=!unlocked()||busy||s.money<multiPrice();
 $("luckBuy").textContent=unlocked()?fmt(luckPrice()):"🔒 Lv.7";
 $("multiBuy").textContent=unlocked()?fmt(multiPrice()):"🔒 Lv.7";
 $("continuous").disabled=!unlocked();
 $("continuousLabel").textContent=unlocked()?"連続ドロップ（ON時は長押し）":"🔒 Lv.7で連続ドロップ解放";
 [...$("bet").options].forEach(o=>o.disabled=Number(o.value)>cap());
 if(Number($("bet").value)>cap())$("bet").value=String(cap());
 $("drop").disabled=busy\vert{}\vert{}s.money<Number($("bet").value);
}
function draw(){
 ctx.clearRect(0,0,600,580);
 const g=ctx.createLinearGradient(0,0,0,580);g.addColorStop(0,"#101a30");g.addColorStop(1,"#080d18");ctx.fillStyle=g;ctx.fillRect(0,0,600,580);
 ctx.strokeStyle="#53698d";ctx.lineWidth=5;ctx.beginPath();ctx.moveTo(35,25);ctx.lineTo(35,480);ctx.lineTo(565,480);ctx.lineTo(565,25);ctx.stroke();
 for(let r=0;r<9;r++){let y=80+r*43,n=8+r%2;for(let i=0;i<n;i++){let x=85+i*430/(n-1)+(r%2?0:12);ctx.beginPath();ctx.arc(x,y,5,0,7);ctx.fillStyle="#91a8ce";ctx.fill()}}
 for(let i=0;i<5;i++){ctx.fillStyle=["#ffd45c","#ff647c","#5bffb6","#5bffb6","#ffd45c"][i];ctx.font="bold 23px Arial";ctx.textAlign="center";ctx.fillText(["×10","−20%","×2","×1.5","×5"][i],88+i*106,540)}
}
function animate(zone){
 return new Promise(resolve=>{
  let start=performance.now(),duration=850;
  function frame(now){
   let p=Math.min(1,(now-start)/duration),e=p*p*(3-2*p);
   draw();
   let x=300+(88+zone*106-300)*e+Math.sin(p*35)*9*(1-p),y=30+450*p;
   ctx.beginPath();ctx.arc(x,y,10,0,7);ctx.fillStyle="#ffe28a";ctx.shadowColor="#ffbf32";ctx.shadowBlur=18;ctx.fill();ctx.shadowBlur=0;
   if(p<1)requestAnimationFrame(frame);else resolve();
  }requestAnimationFrame(frame);
 });
}
function choose(continuous){
 let base=[.13,.24,.25,.23,.15],boost=Math.min(.04*s.luck,.18)+(continuous?.035:0);
 let w=[base[0]+boost*.3,Math.max(.035,base[1]-boost),base[2]+boost*.15,base[3]+boost*.15,base[4]+boost*.4];
 let x=Math.random()*w.reduce((a,b)=>a+b,0);
 for(let i=0;i<5;i++){x-=w[i];if(x<=0)return i}return 4;
}
async function dropOne(continuous=false){
 if(busy)return false;
 let bet=continuous?100:Number($("bet").value);
 if(bet>cap()&&!continuous){say("掛け金上限を超えています");return false}
 if(s.money<bet){say("コインが足りません");return false}
 busy=true;s.money-=bet;s.plays++;save();update();
 let old=lv(),zone=choose(continuous);
 await animate(zone);
 let mult=[10,.8,2,1.5,5][zone];
 mult=zone===1?mult:mult*(1+s.multi*.1);
 let payout=Math.floor(bet*mult*rb());s.money+=payout;
 let names=["×10","−20%","×2","×1.5","×5"];
 say(`${names[zone]}！ 払戻 ${fmt(payout)}`);
 log(`${names[zone]} / 賭け${fmt(bet)} / 払戻${fmt(payout)}`);
 if(lv()>old){say("🎉 LEVEL UP！ Lv."+lv());log("🎉 Lv."+lv()+" 到達！")}
 busy=false;save();update();draw();return true;
}
async function holdStart(e){
 e.preventDefault();if(busy)return;
 if(!$("continuous").checked){dropOne(false);return}
 if(!unlocked()){say("🔒 Lv.7で解放！");return}
 if(holding)return;holding=true;
 while(holding){
  if(s.money<100){holding=false;say("コイン不足");break}
  await dropOne(true);
  if(holding)await new Promise(r=>setTimeout(r,100));
 }
}
function stop(){holding=false}
$("drop").addEventListener("pointerdown",holdStart);
$("drop").addEventListener("pointerup",stop);
$("drop").addEventListener("pointercancel",stop);
$("drop").addEventListener("pointerleave",stop);
window.addEventListener("pointerup",stop);window.addEventListener("blur",stop);
$("maxbet").onclick=()=>{$("bet").value=String(cap());update()};
$("save").onclick=()=>{save();say("💾 セーブしました")};
$("luckBuy").onclick=()=>buy("luck");
$("multiBuy").onclick=()=>buy("multi");
function buy(kind){
 if(!unlocked()){say("🔒 Lv.7で解放！");return}
 let price=kind==="luck"?luckPrice():multiPrice();
 if(s.money<price){say("コイン不足");return}
 s.money-=price;s[kind]++;save();update();say("強化成功！");log("強化Lv."+s[kind]+" 購入");
}
$("rebirthBtn").onclick=()=>{
 if(s.money<need()||busy)return;
 if(!confirm("転生しますか？\n所持金・レベル・強化レベルがリセットされます。\n転生ボーナス＋5%。"))return;
 s.money=0;s.plays=0;s.luck=0;s.multi=0;s.rebirths++;
 save();update();draw();say("♻ 転生成功！ ×"+rb().toFixed(2));log("転生"+s.rebirths+"回目");
};
$("reset").onclick=()=>{
 if(confirm("すべてのデータをリセットしますか？")){
  s={...initial};save();update();draw();say("リセットしました");$("log").textContent="ゲーム開始！";
 }
};
$("bet").onchange=update;
$("continuous").onchange=()=>say($("continuous").checked?"連続ドロップON":"連続ドロップOFF");
update();draw();
})();
</script>
</body>
</html>
