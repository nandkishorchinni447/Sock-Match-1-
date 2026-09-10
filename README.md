html = r'''<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no">
<title>Sock Match Panic</title>
<style>
*{box-sizing:border-box;-webkit-tap-highlight-color:transparent}
html,body{margin:0;width:100%;height:100%;overflow:hidden;font-family:system-ui,-apple-system,Segoe UI,sans-serif;background:#10131a;color:#f8fafc}
button{font:inherit}
.screen{position:absolute;inset:0;display:none}
.screen.active{display:flex}
#home{align-items:center;justify-content:center;text-align:center;background:radial-gradient(circle at 50% 20%,#2b3040 0,#151923 45%,#090b10 100%)}
.card{width:min(92vw,520px);padding:30px 22px}
.logo{font-size:clamp(42px,12vw,76px);filter:drop-shadow(0 8px 18px #0008)}
h1{margin:8px 0 4px;font-size:clamp(30px,8vw,52px);letter-spacing:-2px}
.sub{color:#aab2c2;margin:0 0 28px;font-size:15px}
.btn{border:0;border-radius:16px;padding:16px 25px;font-weight:800;color:#0b0d12;background:#fff;cursor:pointer;box-shadow:0 8px 0 #aeb5c0;transition:.12s;touch-action:manipulation}
.btn:active{transform:translateY(5px);box-shadow:0 3px 0 #aeb5c0}
#game{flex-direction:column;background:linear-gradient(#1b202b,#10131a)}
.top{height:84px;display:flex;align-items:center;justify-content:space-between;padding:10px 16px;position:relative;z-index:5}
.stat{text-align:center;min-width:82px}.label{font-size:11px;color:#9099aa;text-transform:uppercase;letter-spacing:1px}.value{font-size:23px;font-weight:900}
.timer{position:absolute;left:50%;top:12px;transform:translateX(-50%);width:105px;height:7px;border-radius:9px;background:#303746;overflow:hidden}.timer i{display:block;height:100%;width:100%;background:#fff;border-radius:9px;transform-origin:left}
#arena{position:relative;flex:1;overflow:hidden;touch-action:none}
.sock{position:absolute;width:76px;height:105px;border-radius:38px 38px 30px 30px 18px;background:#eee;box-shadow:0 10px 16px #0005;transform:rotate(var(--r));cursor:grab;touch-action:none;user-select:none;display:flex;align-items:center;justify-content:center;overflow:hidden}
.sock:before{content:"";position:absolute;left:-15px;bottom:-4px;width:47px;height:49px;background:inherit;border-radius:20px 8px 18px 18px;transform:rotate(-32deg);box-shadow:inset -5px -4px 0 #0001}
.sock .cuff{position:absolute;top:0;left:0;right:0;height:18px;background:#0002}
.pattern{position:absolute;inset:20px 0 0;opacity:.9}
.stripe .pattern{background:repeating-linear-gradient(0deg,var(--p) 0 11px,#fff 11px 21px)}
.dot .pattern{background-image:radial-gradient(var(--p) 5px,transparent 6px);background-size:19px 19px}
.wave .pattern{background:repeating-linear-gradient(135deg,var(--p) 0 7px,#fff 7px 16px)}
.star .pattern{background-image:radial-gradient(var(--p) 3px,transparent 4px);background-size:22px 22px}
.sock.selected{outline:4px solid #fff;outline-offset:4px;z-index:20;transform:scale(1.08) rotate(var(--r))}
.basket{height:105px;position:relative;margin:0 16px 18px;border-radius:24px 24px 38px 38px;background:linear-gradient(180deg,#303747,#202531);border:4px solid #454d5d;box-shadow:inset 0 10px 18px #0005,0 10px 25px #0005;display:flex;align-items:center;justify-content:center;color:#8f98a9;font-size:13px;font-weight:800;letter-spacing:1px}
.basket:before{content:"";position:absolute;left:9%;right:9%;top:-11px;height:18px;border-radius:50%;border:4px solid #454d5d;background:#252b37}
.pop{position:absolute;pointer-events:none;font-size:25px;font-weight:1000;animation:pop .55s ease-out forwards;z-index:50}
@keyframes pop{0%{opacity:0;transform:translateY(8px) scale(.7)}30%{opacity:1}100%{opacity:0;transform:translateY(-55px) scale(1.15)}}
.shake{animation:shake .25s}
@keyframes shake{25%{transform:translateX(-7px)}50%{transform:translateX(7px)}75%{transform:translateX(-4px)}}
#over{align-items:center;justify-content:center;text-align:center;background:radial-gradient(circle at 50% 25%,#2a303c,#10131a 62%)}
.overCard{width:min(90vw,450px);padding:30px}
.overCard h2{font-size:46px;margin:0 0 5px}
.score{font-size:62px;font-weight:1000;margin:8px 0}
.best{color:#aab2c2;margin-bottom:25px}
.actions{display:flex;gap:12px;justify-content:center;flex-wrap:wrap}.actions .btn{min-width:145px}
@media(max-width:380px){.sock{width:66px;height:94px}.top{height:76px}.basket{height:90px;margin-bottom:10px}}
</style>
</head>
<body>
<section id="home" class="screen active">
  <div class="card">
    <div class="logo">🧦🧺</div>
    <h1>SOCK MATCH<br>PANIC</h1>
    <p class="sub">Match the pairs before the laundry pile gets out of control.</p>
    <button class="btn" id="play">START MATCHING</button>
  </div>
</section>

<section id="game" class="screen">
  <div class="top">
    <div class="stat"><div class="label">Score</div><div class="value" id="score">0</div></div>
    <div class="stat"><div class="label">Combo</div><div class="value" id="combo">0</div></div>
    <div class="stat"><div class="label">Pairs</div><div class="value" id="pairs">0</div></div>
    <div class="timer"><i id="bar"></i></div>
  </div>
  <div id="arena"></div>
  <div class="basket">LAUNDRY BASKET</div>
</section>

<section id="over" class="screen">
  <div class="overCard">
    <div style="font-size:48px">🧦💥</div>
    <h2>TIME'S UP!</h2>
    <div class="score" id="final">0</div>
    <div class="best" id="best">Best: 0</div>
    <div class="actions">
      <button class="btn" id="again">PLAY AGAIN</button>
      <button class="btn" id="homeBtn">HOME</button>
    </div>
  </div>
</section>

<script>
const $=id=>document.getElementById(id);
const screens={home:$('home'),game:$('game'),over:$('over')};
const patterns=[
  {type:'stripe',base:'#f4f4f5',p:'#ef4444'},
  {type:'dot',base:'#60a5fa',p:'#fef08a'},
  {type:'wave',base:'#fde68a',p:'#7c3aed'},
  {type:'star',base:'#86efac',p:'#db2777'},
  {type:'stripe',base:'#c4b5fd',p:'#0f766e'},
  {type:'dot',base:'#fb7185',p:'#1e3a8a'}
];
let socks=[],selected=null,score=0,combo=0,pairs=0,timeLeft=45,last=0,running=false,raf=0,uid=0;
let best=+localStorage.getItem('sockBest')||0;

function show(s){Object.values(screens).forEach(x=>x.classList.remove('active'));screens[s].classList.add('active')}
function rand(a,b){return Math.random()*(b-a)+a}
function shuffle(a){return a.sort(()=>Math.random()-.5)}

function start(){
  cancelAnimationFrame(raf); socks=[]; selected=null; score=0;combo=0;pairs=0;timeLeft=45;running=true;
  $('score').textContent=0;$('combo').textContent=0;$('pairs').textContent=0;$('bar').style.width='100%';
  $('arena').innerHTML=''; show('game');
  // Start with three pairs. More socks enter as the player succeeds.
  for(let i=0;i<3;i++) addPair();
  last=performance.now(); raf=requestAnimationFrame(loop);
}
function addPair(){
  const data=patterns[Math.floor(Math.random()*patterns.length)];
  const id=uid++;
  createSock(data,id);createSock(data,id);
}
function createSock(data,pair){
  const el=document.createElement('div');el.className='sock '+data.type;
  el.dataset.pair=pair;el.dataset.id=uid+Math.random();
  el.style.background=data.base;el.style.setProperty('--p',data.p);
  el.style.setProperty('--r',rand(-18,18)+'deg');
  el.innerHTML='<span class="cuff"></span><span class="pattern"></span>';
  const ar=$('arena'), w=ar.clientWidth,h=ar.clientHeight;
  el.style.left=rand(8,Math.max(8,w-84))+'px';
  el.style.top=rand(10,Math.max(10,h-125))+'px';
  ar.appendChild(el); socks.push(el);
  el.addEventListener('pointerdown',pick);
}
function pick(e){
  if(!running)return;e.preventDefault();
  const el=e.currentTarget;
  if(selected===el){el.classList.remove('selected');selected=null;return}
  if(selected){
    if(selected.dataset.pair===el.dataset.pair){
      match(selected,el);
    }else{
      wrong(el);
    }
  }else{
    selected=el;el.classList.add('selected');
  }
}
function match(a,b){
  const x=(a.getBoundingClientRect().left+b.getBoundingClientRect().left)/2;
  const y=(a.getBoundingClientRect().top+b.getBoundingClientRect().top)/2;
  combo++;pairs++;score+=100+combo*20;timeLeft=Math.min(45,timeLeft+2.2);
  $('score').textContent=score;$('combo').textContent=combo;$('pairs').textContent=pairs;
  pop(x,y,'+'+(100+combo*20));
  a.remove();b.remove();socks=socks.filter(x=>x!==a&&x!==b);selected=null;
  // Gradually increase density.
  const extra=pairs<4?1:pairs<8?2:3;
  for(let i=0;i<extra;i++)addPair();
}
function wrong(el){
  combo=0;$('combo').textContent=0;timeLeft=Math.max(0,timeLeft-2.5);
  el.classList.remove('selected');selected=null;
  $('arena').classList.remove('shake');void $('arena').offsetWidth;$('arena').classList.add('shake');
  const r=el.getBoundingClientRect();pop(r.left+r.width/2,r.top,'NOPE!');
}
function pop(x,y,text){
  const p=document.createElement('div');p.className='pop';p.textContent=text;
  p.style.left=(x-25)+'px';p.style.top=(y-20)+'px';document.body.appendChild(p);
  setTimeout(()=>p.remove(),600);
}
function loop(now){
  if(!running)return;
  const dt=(now-last)/1000;last=now;timeLeft-=dt;
  $('bar').style.width=Math.max(0,timeLeft/45*100)+'%';
  if(timeLeft<=0){end();return}
  raf=requestAnimationFrame(loop);
}
function end(){
  running=false;cancelAnimationFrame(raf);
  if(selected)selected.classList.remove('selected');selected=null;
  best=Math.max(best,score);localStorage.setItem('sockBest',best);
  $('final').textContent=score;$('best').textContent='Best: '+best;show('over');
}
$('play').onclick=start;$('again').onclick=start;$('homeBtn').onclick=()=>{running=false;cancelAnimationFrame(raf);show('home')};
</script>
</body>
</html>'''

path=Path("/mnt/data/sock_match_panic.html")
path.write_text(html, encoding="utf-8")
print(f"Created: {path}")
