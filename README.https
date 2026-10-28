<!DOCTYPE html>
<html lang="ko"><head><meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>푸리에 변환 시각화</title>
<style>
:root{--bg:#f6f6f3;--panel:#fff;--fg:#1c1c1e;--mut:#6b6b70;--grid:#dcdcd6;--acc:#c98a00;--red:#e0323c;--bd:#d8d8d2;color-scheme:light;box-sizing:border-box;padding-top:env(safe-area-inset-top,0px);padding-bottom:env(safe-area-inset-bottom,0px)}
@media (prefers-color-scheme:dark){:root:not([data-theme="light"]){--bg:#101114;--panel:#1a1b20;--fg:#ececf0;--mut:#9a9aa3;--grid:#33343b;--acc:#f2c744;--red:#ff5560;--bd:#30313a;color-scheme:dark}}
:root[data-theme="dark"]{--bg:#101114;--panel:#1a1b20;--fg:#ececf0;--mut:#9a9aa3;--grid:#33343b;--acc:#f2c744;--red:#ff5560;--bd:#30313a;color-scheme:dark}
*{box-sizing:border-box}
html{scroll-padding-top:env(safe-area-inset-top,0px)}
body{margin:0;background:var(--bg);color:var(--fg);font:14px/1.5 -apple-system,"Apple SD Gothic Neo","Noto Sans KR","Malgun Gothic",sans-serif;padding:10px;display:flex;flex-direction:column;gap:10px;height:100dvh;overflow:hidden}[hidden]{display:none!important}
.card{background:var(--panel);border:1px solid var(--bd);border-radius:12px;padding:8px;display:flex;flex-direction:column;min-height:0}
.layout{display:grid;grid-template-columns:1fr 140px;gap:10px;flex:1.3 1 0;min-height:0}.layout>*{min-height:0}
.main{display:flex;flex-direction:column;gap:10px;min-width:0}.f1{flex:1 1 0}.f2{flex:1.5 1 0}
.side{display:flex;flex-direction:column;gap:4px;overflow:auto}
.side h3{margin:0;font-size:13px;text-align:center}
.nb{display:flex;align-items:center;gap:6px;padding:2px 8px;border:1px solid var(--bd);border-radius:8px;background:var(--bg);color:var(--fg);cursor:pointer;font:inherit;font-size:12px;text-align:left}
.nb i{width:10px;height:10px;border-radius:50%;flex:none;background:var(--grid)}
.nb small{margin-left:auto;color:var(--mut);font-size:11px}
.nb.on{border-color:var(--c);background:color-mix(in srgb,var(--c) 18%,var(--panel))}
.nb.on i{background:var(--c)}
.nb:disabled{opacity:.6;cursor:default}
button,select,input[type=number]{font:inherit;background:var(--bg);color:var(--fg);border:1px solid var(--bd);border-radius:8px;padding:4px 10px;cursor:pointer}
input[type=number]{width:90px;cursor:text}
button.on{background:var(--acc);color:#111;border-color:var(--acc)}
.cv{position:relative;flex:1 1 0;min-height:0}canvas{position:absolute;inset:0;width:100%;height:100%}
#stack{flex:1 1 0;min-height:0;display:flex;flex-direction:column;gap:2px}#stack .cv{min-height:30px}
.ph{color:var(--mut);text-align:center;padding:26px 0}
.row{display:flex;gap:8px;align-items:center;flex-wrap:wrap;margin:3px 0}
.row input[type=range]{flex:1;min-width:160px}
.val{font-variant-numeric:tabular-nums;color:var(--acc);font-weight:600}
.bot{display:grid;grid-template-columns:1fr 1.2fr;gap:10px;flex:1 1 0;min-height:0}
@media(max-width:820px){body{height:auto;overflow:auto}.layout,.bot{grid-template-columns:1fr;flex:none}.main .f1{flex:none;height:200px}.main .f2{flex:none;height:320px}.bot .card{height:320px}.side{order:-1}}
</style></head><body>
<div class="layout">
 <div class="main">
  <div class="card f1"><div class="cv"><canvas id="cT"></canvas></div></div>
  <div class="card f2"><div id="stack"></div><div class="ph" id="ph" hidden>원함수가 숨겨져 있어요</div></div>
  <div class="card">
   <div class="row"><b>진동수 f</b><input type="range" id="fs" min="0" max="20" step="0.001" value="4"><input type="number" id="fn" min="0" max="20" step="0.001" value="4"><span class="val">Hz</span></div>
   <div class="row"><span>미세 조절</span>
    <button data-d="-0.1">−0.1</button><button data-d="-0.01">−0.01</button><button data-d="-0.001">−0.001</button>
    <button data-d="0.001">+0.001</button><button data-d="0.01">+0.01</button><button data-d="0.1">+0.1</button></div>
   <div class="row"><button id="wind">▶ 감기 애니메이션</button><button id="sweep">⇄ 진동수 스윕</button><button id="reset">초기화</button>
    <span>속도</span><select id="spd"><option value="0.5">0.5×</option><option value="1" selected>1×</option><option value="2">2×</option></select></div>
  </div>
 </div>
 <aside class="card side">
  <h3>계이름</h3>
  <div id="nl" style="display:flex;flex-direction:column;gap:4px"></div>
  <select id="oct"><option value="3">옥타브 3</option><option value="4" selected>옥타브 4</option><option value="5">옥타브 5</option></select>
  <button id="snd">🔊 소리 재생</button>
  <button id="hide">🙈 원함수 숨기기</button>
 </aside>
</div>
<div class="bot">
 <div class="card"><div class="cv"><canvas id="cP"></canvas></div></div>
 <div class="card"><div class="cv"><canvas id="cF"></canvas></div></div>
</div>
<script>
const $=id=>document.getElementById(id);
const NAMES=["도","도♯","레","레♯","미","파","파♯","솔","솔♯","라","라♯","시"];
const T=4,FMAX=20,TWO=2*Math.PI;
const st={act:NAMES.map((_,i)=>i==0||i==4),oct:4,f:4,prog:T,wind:false,sweep:false,hide:false,off:1};
const col=i=>`hsl(${i*30},70%,50%)`;
const freq=i=>440*Math.pow(2,(12*(st.oct+1)+i-69)/12);
const vf=i=>freq(i)/65.406;
const act=()=>st.act.map((a,i)=>a?i:-1).filter(i=>i>=0);
const g=t=>{let s=st.off;for(const i of act())s+=Math.cos(TWO*vf(i)*t);return s;};
function F(f,te){const N=500,dt=te/N;let re=0,im=0;for(let k=0;k<N;k++){const t=(k+.5)*dt,v=g(t),th=TWO*f*t;re+=v*Math.cos(th);im-=v*Math.sin(th);}return [re/N,im/N];}
let cache=null,dirty=true;
function curve(){if(!dirty&&cache)return cache;const a=[];for(let k=0;k<=400;k++)a.push(F(FMAX*k/400,T)[0]);cache=a;dirty=false;return a;}
// 계이름 버튼
const nl=$("nl");
NAMES.forEach((n,i)=>{const b=document.createElement("button");b.className="nb";b.style.setProperty("--c",col(i));b.dataset.i=i;b.innerHTML=`<i></i>${n}<small></small>`;nl.appendChild(b);});
nl.onclick=e=>{const b=e.target.closest(".nb");if(!b||st.hide)return;const i=+b.dataset.i;st.act[i]=!st.act[i];changed();};
function renderNotes(){[...nl.children].forEach((b,i)=>{const on=st.act[i]&&!st.hide;b.classList.toggle("on",on);b.disabled=st.hide;b.lastChild.textContent=vf(i).toFixed(1)+"Hz";});
 $("oct").disabled=st.hide;$("hide").classList.toggle("on",st.hide);$("hide").textContent=st.hide?"👁 원함수 보이기":"🙈 원함수 숨기기";
 $("ph").hidden=!st.hide;$("stack").hidden=st.hide;}
function changed(){dirty=true;buildStack();renderNotes();audio();draw();}
$("oct").onchange=e=>{st.oct=+e.target.value;changed();};
$("hide").onclick=()=>{st.hide=!st.hide;changed();};
// 개별 그래프 캔버스
function buildStack(){const s=$("stack");s.innerHTML="";act().forEach(i=>{const w=document.createElement("div");w.className="cv";const c=document.createElement("canvas");c.dataset.i=i;w.appendChild(c);s.appendChild(w);});
 if(!act().length){s.innerHTML='<div class="ph">선택된 음이 없어요</div>';}}
// 그리기
function setup(c){const r=c.getBoundingClientRect(),d=devicePixelRatio||1;c.width=r.width*d;c.height=r.height*d;const x=c.getContext("2d");x.setTransform(d,0,0,d,0,0);return [x,r.width,r.height];}
const css=n=>getComputedStyle(document.documentElement).getPropertyValue(n).trim();
function line(x,pts,color,w){x.beginPath();pts.forEach((p,i)=>i?x.lineTo(p[0],p[1]):x.moveTo(p[0],p[1]));x.strokeStyle=color;x.lineWidth=w;x.stroke();}
function timeGraph(c,fn,color,w,ym,o){
 const [x,W,H]=setup(c),gr=css("--grid"),mu=css("--mut");
 const L=34,R=10,Tp=8,B=o.axis?20:6,iw=W-L-R,ih=H-Tp-B;
 const X=t=>L+t/T*iw,Y=v=>Tp+ih/2-v/ym*ih/2;
 x.strokeStyle=gr;x.lineWidth=1;x.fillStyle=mu;x.font="11px sans-serif";x.textAlign="center";
 for(let t=0;t<=T;t++){x.beginPath();x.moveTo(X(t),Tp);x.lineTo(X(t),Tp+ih);x.stroke();if(o.axis)x.fillText(t+"s",X(t),H-5);}
 x.textAlign="right";for(let v=-Math.floor(ym);v<=ym;v++){x.beginPath();x.moveTo(L,Y(v));x.lineTo(L+iw,Y(v));x.stroke();x.fillText(v,L-4,Y(v)+4);}
 const p=[];for(let k=0;k<=600;k++){const t=T*k/600;p.push([X(t),Y(fn(t))]);}line(x,p,color,w);
 if(o.title){x.fillStyle=color;x.font="bold 13px sans-serif";x.textAlign="left";x.fillText(o.title,L+6,Tp+14);}
 if(o.period&&st.f>0.05){x.setLineDash([5,4]);x.strokeStyle=css("--red");x.lineWidth=1.3;
  for(let k=0;k/st.f<=T+1e-9;k++){const t=k/st.f;x.beginPath();x.moveTo(X(t),Tp);x.lineTo(X(t),Tp+ih);x.stroke();}
  x.setLineDash([]);x.fillStyle=css("--red");x.font="11px sans-serif";x.textAlign="right";x.fillText("주기 1/f = "+(1/st.f).toFixed(3)+"s",L+iw,Tp+12);}
 if(o.play&&st.prog<T){x.strokeStyle=css("--fg");x.lineWidth=1.5;x.beginPath();x.moveTo(X(st.prog),Tp);x.lineTo(X(st.prog),Tp+ih);x.stroke();}
}
function drawP(){
 const [x,W,H]=setup($("cP")),gr=css("--grid"),mu=css("--mut"),fg=css("--fg");
 let gm=0;for(let k=0;k<200;k++)gm=Math.max(gm,Math.abs(g(T*k/199)));gm=Math.max(gm,1.2)*1.1;
 const s=Math.min(W,H)/2/gm,cx=W/2,cy=H/2;
 x.strokeStyle=gr;x.lineWidth=1;x.fillStyle=mu;x.font="11px sans-serif";x.textAlign="center";
 for(let v=-Math.floor(gm);v<=gm;v++){x.beginPath();x.moveTo(cx+v*s,0);x.lineTo(cx+v*s,H);x.stroke();x.beginPath();x.moveTo(0,cy-v*s);x.lineTo(W,cy-v*s);x.stroke();if(v)x.fillText(v,cx+v*s,cy+12);}
 x.strokeStyle=mu;x.beginPath();x.moveTo(0,cy);x.lineTo(W,cy);x.moveTo(cx,0);x.lineTo(cx,H);x.stroke();
 x.setLineDash([6,5]);x.strokeStyle=fg;x.lineWidth=1.6;x.beginPath();x.arc(cx,cy,s,0,TWO);x.stroke();x.setLineDash([]);
 const te=Math.max(st.prog,1e-6),p=[];
 for(let k=0;k<=1500;k++){const t=te*k/1500,v=g(t),th=TWO*st.f*t;p.push([cx+v*Math.cos(th)*s,cy+v*Math.sin(th)*s]);}
 line(x,p,css("--acc"),1.8);
 const m=F(st.f,te);x.fillStyle=css("--red");x.beginPath();x.arc(cx+m[0]*s,cy-m[1]*s,6,0,TWO);x.fill();
 x.fillStyle=fg;x.textAlign="left";x.fillText("무게중심 x = "+m[0].toFixed(3),8,16);
}
function drawF(){
 const [x,W,H]=setup($("cF")),gr=css("--grid"),mu=css("--mut");
 const a=curve();let lo=Math.min(0,...a),hi=Math.max(...a,0.5);const pad=(hi-lo)*.1;lo-=pad;hi+=pad;
 const L=38,R=10,Tp=10,B=24,iw=W-L-R,ih=H-Tp-B;
 const X=f=>L+f/FMAX*iw,Y=v=>Tp+(hi-v)/(hi-lo)*ih;
 x.strokeStyle=gr;x.fillStyle=mu;x.font="11px sans-serif";x.textAlign="center";
 for(let f=0;f<=FMAX;f+=2){x.beginPath();x.moveTo(X(f),Tp);x.lineTo(X(f),Tp+ih);x.stroke();x.fillText(f,X(f),H-8);}
 x.textAlign="right";const step=Math.max(.5,Math.round((hi-lo)/6*2)/2);
 for(let v=Math.ceil(lo/step)*step;v<=hi;v+=step){x.beginPath();x.moveTo(L,Y(v));x.lineTo(L+iw,Y(v));x.stroke();x.fillText(v.toFixed(1),L-4,Y(v)+4);}
 x.strokeStyle=mu;x.beginPath();x.moveTo(L,Y(0));x.lineTo(L+iw,Y(0));x.stroke();
 line(x,a.map((v,k)=>[X(FMAX*k/400),Y(v)]),css("--acc"),2);
 x.strokeStyle=css("--red");x.setLineDash([5,4]);x.beginPath();x.moveTo(X(st.f),Tp);x.lineTo(X(st.f),Tp+ih);x.stroke();x.setLineDash([]);
 const m=F(st.f,T)[0];x.fillStyle=css("--red");x.beginPath();x.arc(X(st.f),Y(m),5,0,TWO);x.fill();
 x.fillStyle=mu;x.textAlign="right";x.fillText("f (Hz)",W-R,H-8);
}
function draw(){
 const f=st.f.toFixed(3);if(document.activeElement!==$("fn"))$("fn").value=f;$("fs").value=st.f;
 let ym=1.2;for(let k=0;k<200;k++)ym=Math.max(ym,Math.abs(g(T*k/199)));
 timeGraph($("cT"),g,css("--acc"),2.6,Math.ceil(ym*1.05),{axis:true,period:true,play:true});
 if(!st.hide)document.querySelectorAll("#stack canvas").forEach(c=>{const i=+c.dataset.i;
  timeGraph(c,t=>Math.cos(TWO*vf(i)*t),col(i),1.6,1.2,{title:NAMES[i]+" ("+vf(i).toFixed(1)+"Hz)",period:true,play:true});});
 drawP();drawF();
}
function setF(v){st.f=Math.min(FMAX,Math.max(0,Math.round(v*1000)/1000));st.sweep=false;draw();}
$("fs").oninput=e=>setF(+e.target.value);
$("fn").oninput=e=>{if(e.target.value!=="")setF(+e.target.value);};
$("fn").onblur=draw;
document.querySelectorAll("[data-d]").forEach(b=>b.onclick=()=>setF(st.f+ +b.dataset.d));
// 애니메이션 (느리게)
let last=0;const spd=()=>+$("spd").value;
$("wind").onclick=()=>{st.prog=0;st.wind=true;st.sweep=false;last=0;requestAnimationFrame(loop);};
$("sweep").onclick=()=>{st.sweep=!st.sweep;st.prog=T;st.wind=false;if(st.sweep){st.f=0;last=0;requestAnimationFrame(loop);}};
$("reset").onclick=()=>{st.prog=T;st.wind=false;st.sweep=false;draw();};
function loop(ts){if(!st.wind&&!st.sweep)return;const dt=last?Math.min((ts-last)/1000,.05):0;last=ts;
 if(st.wind){st.prog+=dt*T/20*spd();if(st.prog>=T){st.prog=T;st.wind=false;}}
 if(st.sweep){st.f+=dt*FMAX/40*spd();if(st.f>=FMAX){st.f=FMAX;st.sweep=false;}}
 draw();requestAnimationFrame(loop);}
// 소리
let ac=null,oscs=[],master=null,playing=false;
function audio(){if(!playing)return;oscs.forEach(o=>o.stop());oscs=[];const A=act();
 A.forEach(i=>{const o=ac.createOscillator(),gn=ac.createGain();o.frequency.value=freq(i);gn.gain.value=.5/A.length;o.connect(gn).connect(master);o.start();oscs.push(o);});}
$("snd").onclick=()=>{
 if(!playing){try{ac=ac||new (window.AudioContext||window.webkitAudioContext)();master=master||ac.createGain();master.gain.value=.5;master.connect(ac.destination);}catch(e){return;}
  ac.resume();playing=true;$("snd").classList.add("on");$("snd").textContent="⏹ 소리 정지";audio();}
 else{playing=false;oscs.forEach(o=>o.stop());oscs=[];$("snd").classList.remove("on");$("snd").textContent="🔊 소리 재생";}};
addEventListener("resize",draw);
matchMedia("(prefers-color-scheme: dark)").addEventListener("change",draw);
buildStack();renderNotes();draw();
</script></body></html>
