<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>🐻 打工人打卡</title>
<style>
@import url('https://fonts.googleapis.com/css2?family=Noto+Sans+SC:wght@300;400;500;700&display=swap');

* { margin:0; padding:0; box-sizing:border-box; }

body {
  font-family: 'Noto Sans SC','Segoe UI',sans-serif;
  min-height:100vh;
  background:#ffffff;
  display:flex;
  align-items:flex-start;
  justify-content:center;
  padding:40px 20px;
}

.card {
  width:100%;
  max-width:400px;
  background:#fff;
  border-radius:16px;
  padding:36px 28px 24px;
  box-shadow:0 1px 2px rgba(0,0,0,0.02),0 2px 8px rgba(0,0,0,0.02);
  border:1px solid #f5f5f5;
}

/* ── 时钟 ── 更纤细的字体 */
.clock-wrap {
  text-align:center;
  margin-bottom:24px;
}
.clock-wrap .time {
  font-size:2.2rem;
  font-weight:300;
  color:#555;
  letter-spacing:2px;
  line-height:1.2;
  font-variant-numeric:tabular-nums;
}
.clock-wrap .date {
  font-size:0.72rem;
  color:#c8c8c8;
  font-weight:300;
  margin-top:4px;
  letter-spacing:0.5px;
}

/* ── 进度条 ── */
.progress-area { margin-bottom:22px; }
.steps {
  display:flex;
  justify-content:space-between;
  position:relative;
  padding:0 2px;
}
.steps .line {
  position:absolute;
  top:12px; left:20px; right:20px;
  height:1px;
  background:#eee;
  z-index:0;
}
.steps .line-fill {
  position:absolute;
  top:12px; left:20px;
  height:1px;
  background:#c8c8c8;
  z-index:0;
  transition:width 0.4s ease;
}
.step {
  position:relative;
  z-index:1;
  display:flex;
  flex-direction:column;
  align-items:center;
  gap:3px;
}
.step .dot {
  width:24px; height:24px;
  border-radius:50%;
  background:#fff;
  border:1px solid #e0e0e0;
  display:flex;
  align-items:center;
  justify-content:center;
  font-size:0.65rem;
  transition:all 0.3s ease;
  color:#d0d0d0;
}
.step.active .dot { border-color:#bbb; color:#999; }
.step.done .dot { background:#888; border-color:#888; color:#fff; }
.step .label { font-size:0.56rem; color:#d5d5d5; font-weight:400; }
.step.active .label { color:#aaa; }
.step.done .label { color:#888; }
.step .sub { font-size:0.5rem; color:#e0e0e0; margin-top:-1px; }
.step.done .sub { color:#c0c0c0; }

.status {
  text-align:center;
  margin-top:10px;
  font-size:0.7rem;
  color:#c8c8c8;
  font-weight:300;
}
.status strong { font-weight:500; color:#999; }

/* ── 按钮 ── 简洁干净 */
.btn-row {
  display:grid;
  grid-template-columns:1fr 1fr;
  gap:8px;
  margin-bottom:20px;
}
.btn-row button {
  padding:10px 6px;
  border:1px solid #f0f0f0;
  border-radius:10px;
  font-family:inherit;
  font-size:0.75rem;
  font-weight:400;
  cursor:pointer;
  transition:all 0.15s ease;
  display:flex;
  flex-direction:column;
  align-items:center;
  gap:1px;
  background:#fff;
  color:#bbb;
}
.btn-row button:active { transform:scale(0.97); }
.btn-row button:disabled { opacity:0.2; cursor:not-allowed; transform:none!important; }
.btn-row button .icon { font-size:1rem; }
.btn-row button .tlabel { font-size:0.55rem; font-weight:300; color:#ddd; }

.btn-row button.urgent {
  border-color:#e8e0d0;
  color:#aa8a6a;
  background:#fffcf6;
}
.btn-row button.urgent:hover:not(:disabled) { background:#fff8ee; }
.btn-row button.urgent .tlabel { color:#ccbbaa; }

.btn-row button.done { border-color:#f0f0f0; color:#ccc; background:#fafafa; }
.btn-row button.done .tlabel { color:#ddd; }

/* ── 记录 ── */
.records { border-top:1px solid #f8f8f8; padding-top:10px; }
.list { max-height:160px; overflow-y:auto; scrollbar-width:thin; scrollbar-color:#eee transparent; }
.list::-webkit-scrollbar { width:4px; }
.list::-webkit-scrollbar-thumb { background:#eee; border-radius:8px; }

.item {
  display:flex;
  align-items:center;
  gap:6px;
  padding:5px 2px;
  border-bottom:1px solid #fafafa;
  font-size:0.7rem;
  color:#c0c0c0;
  font-weight:300;
}
.item:last-child { border-bottom:none; }
.item .d { color:#d0d0d0; min-width:48px; }
.item .w { color:#ddd; font-size:0.6rem; min-width:14px; }
.item .t { flex:1; font-weight:400; color:#aaa; }
.item .tm { font-weight:400; color:#999; }

.empty { text-align:center; padding:16px 0; color:#e0e0e0; font-size:0.78rem; font-weight:300; }

/* ── Toast ── */
.tc {
  position:fixed; bottom:36px; left:50%; transform:translateX(-50%);
  z-index:999; display:flex; flex-direction:column; gap:6px;
  pointer-events:none; align-items:center;
}
.toast {
  padding:8px 16px; border-radius:8px;
  background:#555; box-shadow:0 2px 10px rgba(0,0,0,0.04);
  font-size:0.75rem; font-weight:400; color:#fff;
  pointer-events:auto;
  animation:up .25s ease,out .25s ease 2s forwards;
  text-align:center; white-space:nowrap; max-width:88vw;
}
@keyframes up { from{opacity:0;transform:translateY(8px)} to{opacity:1;transform:translateY(0)} }
@keyframes out { from{opacity:1} to{opacity:0;transform:translateY(8px)} }

@media (max-width:380px) {
  .card { padding:28px 20px 20px; border-radius:14px; }
  .clock-wrap .time { font-size:1.9rem; }
  .btn-row button { padding:8px 4px; font-size:0.7rem; }
}
</style>
</head>
<body>

<div class="tc" id="tc"></div>

<div class="card">

  <div class="clock-wrap">
    <div class="time" id="clock">--:--:--</div>
    <div class="date" id="date">----年--月--日 周-</div>
  </div>

  <div class="progress-area">
    <div class="steps" id="steps">
      <div class="line"></div>
      <div class="line-fill" id="lineFill" style="width:0%"></div>
      <div class="step" data-id="1"><div class="dot">☀️</div><div class="label">上班</div><div class="sub">9:00</div></div>
      <div class="step" data-id="2"><div class="dot">🍱</div><div class="label">午休</div><div class="sub">12:00</div></div>
      <div class="step" data-id="3"><div class="dot">💼</div><div class="label">回工位</div><div class="sub">13:30</div></div>
      <div class="step" data-id="4"><div class="dot">🌙</div><div class="label">下班</div><div class="sub">18:00</div></div>
    </div>
    <div class="status" id="statusText">今天 <strong>还没有打卡</strong> 哦</div>
  </div>

  <div class="btn-row">
    <button class="btn" id="b1" onclick="p(1)"><span class="icon">☀️</span>上班 <span class="tlabel">9:00</span></button>
    <button class="btn" id="b2" onclick="p(2)"><span class="icon">🍱</span>午休 <span class="tlabel">12:00</span></button>
    <button class="btn" id="b3" onclick="p(3)"><span class="icon">💼</span>回工位 <span class="tlabel">13:30</span></button>
    <button class="btn" id="b4" onclick="p(4)"><span class="icon">🌙</span>下班 <span class="tlabel">18:00</span></button>
  </div>

  <div class="records">
    <div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:4px;padding:0 2px;">
      <span style="font-size:0.55rem;color:#ddd;font-weight:300;">记录</span>
      <button onclick="resetToday()" style="border:none;background:none;cursor:pointer;font-size:0.55rem;color:#d0d0d0;font-family:inherit;padding:2px 4px;">↻ 重置今日</button>
    </div>
    <div class="list" id="list"><div class="empty">还没有打卡记录哦～</div></div>
  </div>

</div>

<script>
const SK='punch_v3';
let r=[];

const SB_URL='https://gjwpbyubgdkvlojvimnh.supabase.co';
const SB_KEY='eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Imdqd3BieXViZ2RrdmxvanZpbW5oIiwicm9sZSI6ImFub24iLCJpYXQiOjE3ODQwMjE4MzIsImV4cCI6MjA5OTU5NzgzMn0.M8FzsfIZAUmrXH6LAfG4BzqAXKE0kze7sPisCphiVGE';
const SB_H={'apikey':SB_KEY,'Authorization':'Bearer '+SB_KEY,'Content-Type':'application/json'};
function localSave(){
  localStorage.setItem(SK,JSON.stringify(r));
}
async function cloudRequest(path,options={}){
  const res=await fetch(SB_URL+'/rest/v1/'+path,{...options,headers:{...SB_H,...(options.headers||{})}});
  if(!res.ok){ throw new Error('HTTP '+res.status+' '+(await res.text())); }
  return res.status===204 ? null : res.json();
}
async function load(){
  try{ r=JSON.parse(localStorage.getItem(SK))||[]; }catch(e){ r=[]; }
  update();
  try{
    const remote=await cloudRequest('records?select=*');
    const merged=new Map([...remote,...r].map(x=>[x.id,x]));
    r=[...merged.values()];
    localSave();
    update();
    if(r.length!==remote.length) await pushCloud();
  }catch(e){
    console.error('同步读取失败',e);
    toast('云端同步失败，请检查网络或数据库权限');
  }
}
async function pushCloud(){
  if(!r.length) return;
  await cloudRequest('records?on_conflict=id',{
    method:'POST',
    headers:{'Prefer':'resolution=merge-duplicates,return=minimal'},
    body:JSON.stringify(r)
  });
}
async function save(){
  localSave();
  try{
    await pushCloud();
  }catch(e){
    console.error('同步写入失败',e);
    toast('已保存到本机，云端同步失败');
  }
}

const S=[
  {id:1,label:'上班',  icon:'☀️', hour:9,  min:0},
  {id:2,label:'午休',  icon:'🍱', hour:12, min:0},
  {id:3,label:'回工位',icon:'💼', hour:13, min:30},
  {id:4,label:'下班',  icon:'🌙', hour:18, min:0},
];
const W=['日','一','二','三','四','五','六'];
const pad=n=>String(n).padStart(2,'0');
const tKey=()=>{const d=new Date(); return d.getFullYear()+'-'+pad(d.getMonth()+1)+'-'+pad(d.getDate());}
const fHM=d=>pad(d.getHours())+':'+pad(d.getMinutes());
let lastDayKey='';

function nextIdx(){
  const n=new Date(),t=n.getHours()*60+n.getMinutes();
  for(let i=0;i<S.length;i++){ const s=S[i]; if(t<s.hour*60+s.min) return i; }
  return -1;
}

function tick(){
  const n=new Date();
  document.getElementById('clock').textContent=pad(n.getHours())+':'+pad(n.getMinutes())+':'+pad(n.getSeconds());
 document.getElementById('date').textContent=n.getFullYear()+'年'+pad(n.getMonth()+1)+'月'+pad(n.getDate())+'日 周'+W[n.getDay()];
  const k=tKey();
  if(lastDayKey&&lastDayKey!==k){ update(); }
  lastDayKey=k;
}

function update(){
  const key=tKey();
  const now=new Date();
  if(now.getDay()===0){
    document.querySelectorAll('.step').forEach(el=>el.classList.remove('done','active'));
    ['b1','b2','b3','b4'].forEach(id=>{const b=document.getElementById(id);b.disabled=true;b.className='btn';});
    document.getElementById('lineFill').style.width='0%';
    document.getElementById('statusText').innerHTML='今天是 <strong>星期日</strong> 好好休息吧';
    renderRec();
    return;
  }
  let done=0;
  for(const s of S){
    const rec=r.find(x=>x.date===key&&x.type===s.id);
    const dot=document.querySelector('.step[data-id="'+s.id+'"]');
    const btn=document.getElementById('b'+s.id);
    dot.classList.remove('done','active');
    btn.disabled=false; btn.className='btn';
    if(rec){
      dot.classList.add('done');
      btn.classList.add('done'); btn.disabled=true;
      btn.innerHTML='<span style="font-size:0.85rem;">✓</span>'+s.label+' <span class="tlabel">'+fHM(new Date(rec.ts))+'</span>';
      done++;
    } else {
      btn.innerHTML='<span class="icon">'+s.icon+'</span>'+s.label+' <span class="tlabel">'+s.hour+':'+pad(s.min)+'</span>';
    }
  }
  const ni=nextIdx();
  if(ni>=0){
    const s=S[ni];
    document.querySelector('.step[data-id="'+s.id+'"]').classList.add('active');
    document.getElementById('b'+s.id).classList.add('urgent');
  }
  document.getElementById('lineFill').style.width=(done/4*100)+'%';
  document.getElementById('statusText').innerHTML=
    done===4 ? '今日全部打卡完成 辛苦了 <strong>打工人</strong>' :
    done===0 ? '今天 <strong>还没有打卡</strong> 哦' :
    '已打卡：<strong>'+S.filter(s=>r.find(x=>x.date===key&&x.type===s.id)).map(s=>s.label).join(' → ')+'</strong>';
  renderRec();
}

function renderRec(){
  const c=document.getElementById('list');
  if(!r.length){ c.innerHTML='<div class="empty">还没有打卡记录哦～</div>'; return; }
  const s=[...r].sort((a,b)=>b.ts-a.ts).slice(0,30);
  c.innerHTML=s.map(x=>{
    const d=new Date(x.ts),sl=S.find(s=>s.id===x.type);
    return '<div class="item"><span class="d">'+x.date.slice(5)+'</span><span class="w">周'+W[d.getDay()]+'</span><span class="t">'+(sl?sl.icon+' '+sl.label:'📌 打卡')+'</span><span class="tm">'+fHM(d)+'</span></div>';
  }).join('');
}

async function resetToday(){
  const key=tKey();
  const before=r.length;
  r=r.filter(x=>x.date!==key);
  if(r.length===before){ toast('今天还没有打卡记录'); return; }
  localSave();
  try{
    await cloudRequest('records?date=eq.'+encodeURIComponent(key),{method:'DELETE'});
  }catch(e){
    console.error('同步删除失败',e);
    toast('本机已重置，云端删除失败');
  }
  update();
  toast('今日打卡已重置');
}

function p(id){
  const day=new Date().getDay();
  if(day===0){ toast('今天是休息日 不用打卡哦'); return; }
  const key=tKey();
  if(r.find(x=>x.date===key&&x.type===id)){
    toast(['已经打过了啦','再点就要生气了','别点了'][Math.floor(Math.random()*3)]);
    return;
  }
  const n=new Date();
  r.push({id:Date.now().toString(36)+Math.random().toString(36).slice(2,5),date:key,type:id,ts:n.getTime()});
  save();
  update();
  toast(txt(id,n));
}

function txt(id,n){
  const s=S.find(x=>x.id===id);
  const t=n.getHours()*60+n.getMinutes(),target=s.hour*60+s.min,d=t-target,hm=fHM(n);
  if(id===1){
    if(d<=5) return '🎉 准时上班 '+hm;
    if(d<=30) return '😅 迟到 '+d+' 分钟';
    return '😰 迟到太多了 '+hm;
  }
  if(id===2) return '🍱 午休快乐～';
  if(id===3){
    if(d<=5) return '💼 准时回工位 '+hm;
    if(d<=20) return '😴 迟到了 '+d+' 分钟';
    return '🐻 太晚了 '+hm;
  }
  if(id===4){
    if(d>=0&&d<=10) return '🎉 下班快乐 '+hm+' 辛苦啦';
    if(d<0) return '🤔 早退 '+Math.abs(d)+' 分钟';
    return '🌙 下班打卡 '+hm;
  }
  return '🐾 '+s.label+' '+hm;
}

function toast(msg){
  const c=document.getElementById('tc');
  const el=document.createElement('div');
  el.className='toast'; el.textContent=msg;
  c.appendChild(el);
  setTimeout(()=>el.remove(),2300);
}

load(); tick(); lastDayKey=tKey(); update();
setInterval(tick,1000);
document.addEventListener('visibilitychange',()=>{
  if(document.visibilityState==='visible') load();
});
</script>

</body>
</html>
