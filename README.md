<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>ExamReady — Online Test Series</title>
<link href="https://fonts.googleapis.com/css2?family=Crimson+Pro:wght@400;600;700&family=JetBrains+Mono:wght@400;700&family=Nunito:wght@400;600;700;800;900&display=swap" rel="stylesheet"/>
<style>
*{box-sizing:border-box;margin:0;padding:0;}
body{font-family:'Nunito',sans-serif;background:#fff;color:#1e293b;}
input,select,textarea,button{font-family:inherit;}
::-webkit-scrollbar{width:5px;}
::-webkit-scrollbar-track{background:#f1f5f9;}
::-webkit-scrollbar-thumb{background:#cbd5e1;border-radius:3px;}

/* SCIENCE BG */
.sci-bg{position:fixed;inset:0;overflow:hidden;pointer-events:none;z-index:0;}
.sci-bg svg{position:absolute;opacity:0.07;}

@keyframes fadeUp{from{opacity:0;transform:translateY(16px);}to{opacity:1;transform:translateY(0);}}
@keyframes fadeIn{from{opacity:0;}to{opacity:1;}}
@keyframes pulse{0%,100%{opacity:1;}50%{opacity:0.3;}}
@keyframes spin{to{transform:rotate(360deg);}}
@keyframes float{0%,100%{transform:translateY(0);}50%{transform:translateY(-8px);}}
.fade{animation:fadeUp 0.4s ease;}
.spinner{width:32px;height:32px;border:3px solid #e2e8f0;border-top-color:#3b82f6;border-radius:50%;animation:spin 0.7s linear infinite;}

/* CARDS */
.card{background:#fff;border:1.5px solid #e2e8f0;border-radius:18px;box-shadow:0 4px 24px rgba(0,0,0,0.07);}
.card-blue{background:linear-gradient(135deg,#eff6ff,#f0f9ff);border:1.5px solid #bfdbfe;}
.card-green{background:linear-gradient(135deg,#f0fdf4,#ecfdf5);border:1.5px solid #bbf7d0;}
.card-purple{background:linear-gradient(135deg,#faf5ff,#f5f3ff);border:1.5px solid #ddd6fe;}

/* INPUTS */
.inp{width:100%;padding:11px 14px;border-radius:10px;border:1.5px solid #e2e8f0;background:#f8fafc;color:#1e293b;font-size:14px;outline:none;transition:border 0.2s;}
.inp:focus{border-color:#3b82f6;background:#fff;}
select.inp{cursor:pointer;}

/* BUTTONS */
.btn-primary{padding:11px 22px;border-radius:10px;border:none;cursor:pointer;background:linear-gradient(135deg,#2563eb,#7c3aed);color:#fff;font-size:14px;font-weight:700;font-family:inherit;transition:opacity 0.2s,transform 0.1s;}
.btn-primary:hover{opacity:0.92;transform:translateY(-1px);}
.btn-secondary{padding:10px 20px;border-radius:10px;border:1.5px solid #e2e8f0;cursor:pointer;background:#fff;color:#64748b;font-size:14px;font-weight:600;font-family:inherit;transition:all 0.2s;}
.btn-secondary:hover{border-color:#94a3b8;color:#374151;}
.btn-green{padding:11px 22px;border-radius:10px;border:none;cursor:pointer;background:linear-gradient(135deg,#16a34a,#15803d);color:#fff;font-size:14px;font-weight:700;font-family:inherit;}
.btn-red{padding:11px 22px;border-radius:10px;border:none;cursor:pointer;background:#dc2626;color:#fff;font-size:14px;font-weight:700;font-family:inherit;}
.btn-purple{padding:11px 22px;border-radius:10px;border:none;cursor:pointer;background:#7c3aed;color:#fff;font-size:14px;font-weight:700;font-family:inherit;}
.btn-orange{padding:11px 22px;border-radius:10px;border:none;cursor:pointer;background:#ea580c;color:#fff;font-size:14px;font-weight:700;font-family:inherit;}

/* LABEL */
.lbl{color:#64748b;font-size:11px;font-weight:700;text-transform:uppercase;letter-spacing:0.8px;display:block;margin-bottom:5px;}

/* TOP BAR */
.topbar{background:#fff;border-bottom:1.5px solid #e2e8f0;padding:10px 16px;display:flex;align-items:center;justify-content:space-between;flex-wrap:wrap;gap:8px;position:sticky;top:0;z-index:100;box-shadow:0 2px 8px rgba(0,0,0,0.06);}

/* OPTION BUTTONS */
.opt-btn{background:#f8fafc;border:2px solid #e2e8f0;border-radius:12px;padding:13px 16px;cursor:pointer;color:#374151;font-size:15px;text-align:left;font-family:inherit;display:flex;align-items:center;gap:12px;width:100%;transition:all 0.15s;}
.opt-btn:hover{border-color:#93c5fd;background:#eff6ff;}
.opt-btn.selected{background:#eff6ff;border-color:#3b82f6;color:#1d4ed8;}
.opt-circle{width:30px;height:30px;border-radius:50%;flex-shrink:0;background:#e2e8f0;border:2px solid #cbd5e1;display:flex;align-items:center;justify-content:center;font-weight:800;font-size:13px;color:#64748b;transition:all 0.15s;}
.opt-btn.selected .opt-circle{background:#3b82f6;border-color:#3b82f6;color:#fff;}
</style>
</head>
<body>

<!-- SCIENCE BACKGROUND SVG -->
<div class="sci-bg" id="sciBg"></div>

<div id="root"></div>

<script type="module">
import{initializeApp}from"https://www.gstatic.com/firebasejs/10.12.0/firebase-app.js";
import{getFirestore,collection,addDoc,getDocs,doc,updateDoc,deleteDoc,query,orderBy,serverTimestamp}from"https://www.gstatic.com/firebasejs/10.12.0/firebase-firestore.js";

const firebaseConfig={
  apiKey:"AIzaSyBBprATihKt7tbhifTaHhicTRGdZI_15mM",
  authDomain:"examready-65881.firebaseapp.com",
  projectId:"examready-65881",
  storageBucket:"examready-65881.firebasestorage.app",
  messagingSenderId:"681767961235",
  appId:"1:681767961235:web:aa929375f50dbd05a60366"
};
const fbApp=initializeApp(firebaseConfig);
const db=getFirestore(fbApp);

const ADMIN_USER="mithilesh18",ADMIN_PASS="233303";

// SCIENCE BG
(function(){
  const symbols=["∫","∑","√","π","∞","Δ","α","β","γ","λ","μ","σ","Ω","θ","φ","ε","∂","∇","±","×","÷","H₂O","CO₂","E=mc²","F=ma","V=IR","PV=nRT","∮","⊕","⊗","H₂SO₄","NaCl","Fe","Cu","Zn","∀","∃","⟹","∈","⊂","Hz","eV","kJ","mol","Pa"];
  const svg=document.getElementById("sciBg");
  let html="";
  for(let i=0;i<60;i++){
    const x=Math.random()*100,y=Math.random()*100;
    const size=Math.random()*18+10;
    const rot=Math.random()*60-30;
    const sym=symbols[Math.floor(Math.random()*symbols.length)];
    const color=["#3b82f6","#7c3aed","#16a34a","#ea580c","#0891b2"][Math.floor(Math.random()*5)];
    html+=`<text style="position:absolute;font-family:'JetBrains Mono',monospace;font-size:${size}px;fill:${color};opacity:0.12;transform:rotate(${rot}deg);" x="${x}%" y="${y}%">${sym}</text>`;
  }
  svg.innerHTML=`<svg width="100%" height="100%" xmlns="http://www.w3.org/2000/svg">${html}</svg>`;
})();

const INSTRUCTIONS=[
  {type:"heading",text:"Welcome to ExamReady — Junior Engineer & Supervisor Test Series"},
  {type:"point",text:"This examination consists of two parts: Part I and Part II."},
  {type:"point",text:"Part I is the Technical / Professional Knowledge Test containing 120 questions worth 120 marks."},
  {type:"point",text:"Part II is the Supervisory Aptitude Test containing 50 questions worth 50 marks."},
  {type:"point",text:"The total duration of the examination is 2 hours (120 minutes)."},
  {type:"point",text:"The total marks for this examination are 170."},
  {type:"point",text:"For every correct answer, you will be awarded +1 mark."},
  {type:"point",text:"For every incorrect answer, -0.25 marks will be deducted (25% negative marking)."},
  {type:"point",text:"Questions left unattempted will carry 0 marks — no penalty for skipping."},
  {type:"point",text:"You can navigate between questions using the Previous and Save & Next buttons."},
  {type:"point",text:"You may mark any question for review using the Mark for Review button."},
  {type:"point",text:"The question palette on the right shows the status of each question at a glance."},
  {type:"symbols",text:"Question palette colour guide:"},
  {type:"point",text:"The live timer is displayed at the top right — keep an eye on it."},
  {type:"point",text:"The examination will auto-submit when the timer reaches zero."},
  {type:"point",text:"You may submit the exam manually at any time using the Submit Test button."},
  {type:"point",text:"Once submitted, you cannot go back and change your answers."},
  {type:"point",text:"Your result will display your score, accuracy, rank and time taken after submission."},
  {type:"point",text:"Please do not refresh or close the browser tab during the examination."},
  {type:"point",text:"Read each question carefully before selecting your answer."},
  {type:"heading",text:"All the best — attempt confidently and manage your time wisely!"},
];

function pad(n){return String(n).padStart(2,"0");}
function formatTime(s){const h=Math.floor(s/3600),m=Math.floor((s%3600)/60),sec=s%60;return h>0?`${pad(h)}:${pad(m)}:${pad(sec)}`:`${pad(m)}:${pad(sec)}`;}
function uid(){return Date.now()+Math.random();}
function makeCaptcha(){const a=Math.floor(Math.random()*9)+1,b=Math.floor(Math.random()*9)+1,p=Math.random()>0.5;return p?{q:`${a} + ${b} = ?`,ans:a+b}:{q:`${Math.max(a,b)} − ${Math.min(a,b)} = ?`,ans:Math.abs(a-b)};}
function el(id){return document.getElementById(id);}

let state={
  screen:"landing",
  student:null,
  questions:[],
  answers:{},
  statuses:{},
  timeLeft:7200,
  startTime:null,
  timeTaken:0,
  timerInterval:null,
  correct:0,incorrect:0,unattempted:0,score:0,
  currentIdx:0,
  showConfirm:false,
  showAdminLogin:false,
  logoClickCount:0,
  logoClickTimer:null,
  captcha:makeCaptcha(),
  instrRevealed:0,
  instrTimer:null,
  adminTab:"add",
  adminFilter:0,
  adminEditId:null,
  adminForm:{question:"",options:["","","",""],correct:0,part:1,solution:""},
  adminBulk:"",adminBulkMsg:"",adminErr:"",
  resultData:null,
  showLeaderboard:false,
  allResults:[],
  showSolutions:false,
};

// FIREBASE
async function loadQuestions(){
  try{const s=await getDocs(query(collection(db,"questions"),orderBy("createdAt","asc")));state.questions=s.docs.map(d=>({id:d.id,...d.data()}));}catch(e){console.error(e);}
}
async function saveQuestion(data){
  try{const r=await addDoc(collection(db,"questions"),{...data,createdAt:serverTimestamp()});state.questions.push({id:r.id,...data});return true;}catch(e){return false;}
}
async function updateQuestion(id,data){
  try{await updateDoc(doc(db,"questions",id),data);state.questions=state.questions.map(q=>q.id===id?{...q,...data}:q);return true;}catch(e){return false;}
}
async function deleteQuestion(id){
  try{await deleteDoc(doc(db,"questions",id));state.questions=state.questions.filter(q=>q.id!==id);return true;}catch(e){return false;}
}
async function saveResult(data){
  try{await addDoc(collection(db,"results"),{...data,submittedAt:serverTimestamp()});}catch(e){}
}
async function loadAllResults(){
  try{const s=await getDocs(query(collection(db,"results"),orderBy("score","desc")));
  const results=s.docs.map(d=>({id:d.id,...d.data()}));
  // Rank by score desc, timeTaken asc
  results.sort((a,b)=>b.score-a.score||(a.timeTaken||0)-(b.timeTaken||0));
  state.allResults=results.map((r,i)=>({...r,rank:i+1}));
  return state.allResults;}catch(e){return[];}
}

// AI SOLUTION
async function generateAISolution(question,options,correct){
  try{
    const prompt=`You are an expert teacher for competitive exams (Junior Engineer/Supervisor level). 
Question: ${question}
Options: A) ${options[0]}  B) ${options[1]}  C) ${options[2]}  D) ${options[3]}
Correct Answer: ${String.fromCharCode(65+correct)}) ${options[correct]}

Provide a clear, concise explanation (3-5 lines) of why this is correct. Include relevant formula or concept if applicable. Be direct and educational.`;
    const res=await fetch("https://api.anthropic.com/v1/messages",{
      method:"POST",
      headers:{"Content-Type":"application/json"},
      body:JSON.stringify({model:"claude-sonnet-4-20250514",max_tokens:400,messages:[{role:"user",content:prompt}]})
    });
    const data=await res.json();
    return data.content?.[0]?.text||"Solution not available.";
  }catch(e){return"Solution not available.";}
}

// RENDER
function render(){
  const root=el("root");root.innerHTML="";
  if(state.showAdminLogin)root.appendChild(renderAdminModal());
  let s;
  if(state.screen==="landing")s=renderLanding();
  else if(state.screen==="signin")s=renderSignIn();
  else if(state.screen==="instructions")s=renderInstructions();
  else if(state.screen==="exam")s=renderExam();
  else if(state.screen==="result")s=renderResult();
  else if(state.screen==="solutions")s=renderSolutions();
  else if(state.screen==="admin")s=renderAdmin();
  else s=renderLanding();
  root.appendChild(s);
}

// 1. LANDING
function renderLanding(){
  const d=document.createElement("div");
  d.style.cssText="min-height:100vh;display:flex;flex-direction:column;align-items:center;justify-content:center;padding:20px;position:relative;z-index:1;";
  d.innerHTML=`
    <div id="logoWrap" style="text-align:center;margin-bottom:50px;cursor:default;user-select:none;animation:float 3s ease-in-out infinite;">
      <div style="display:inline-flex;align-items:center;justify-content:center;width:100px;height:100px;border-radius:28px;background:linear-gradient(135deg,#2563eb,#7c3aed);margin-bottom:20px;font-size:44px;box-shadow:0 20px 60px rgba(37,99,235,0.3);">📋</div>
      <h1 style="color:#1e293b;font-size:46px;font-weight:900;letter-spacing:-2px;font-family:'Crimson Pro',serif;">ExamReady</h1>
      <p style="color:#64748b;margin-top:8px;font-size:14px;letter-spacing:3px;text-transform:uppercase;font-weight:600;">Junior Engineer & Supervisor Test Series</p>
    </div>
    <button id="siBtn" class="btn-primary" style="padding:16px 60px;font-size:18px;border-radius:14px;box-shadow:0 10px 40px rgba(37,99,235,0.25);">Sign In</button>
    <p style="color:#cbd5e1;margin-top:48px;font-size:12px;">&copy; ${new Date().getFullYear()} ExamReady. All rights reserved.</p>
  `;
  d.querySelector("#siBtn").onclick=()=>goTo("signin");
  d.querySelector("#logoWrap").onclick=handleLogoClick;
  return d;
}

function handleLogoClick(){
  state.logoClickCount++;
  clearTimeout(state.logoClickTimer);
  if(state.logoClickCount>=10){state.logoClickCount=0;state.showAdminLogin=true;render();return;}
  state.logoClickTimer=setTimeout(()=>state.logoClickCount=0,2000);
}

// ADMIN MODAL
function renderAdminModal(){
  const o=document.createElement("div");
  o.style.cssText="position:fixed;inset:0;background:rgba(0,0,0,0.5);display:flex;align-items:center;justify-content:center;z-index:9999;backdrop-filter:blur(4px);";
  o.innerHTML=`
    <div class="card fade" style="padding:36px 32px;max-width:360px;width:90%;">
      <div style="text-align:center;margin-bottom:24px;">
        <div style="font-size:32px;margin-bottom:8px;">🔐</div>
        <h2 style="color:#1e293b;font-size:20px;font-weight:800;">Admin Access</h2>
        <p style="color:#94a3b8;margin-top:4px;font-size:13px;">Authorized Personnel Only</p>
      </div>
      <div style="display:flex;flex-direction:column;gap:12px;">
        <input id="aUser" placeholder="Username" class="inp"/>
        <input id="aPass" type="password" placeholder="Password" class="inp"/>
        <p id="aErr" style="color:#ef4444;font-size:13px;display:none;"></p>
        <button id="aLogin" class="btn-primary" style="padding:12px;font-size:15px;">Login</button>
        <button id="aCancel" class="btn-secondary">Cancel</button>
      </div>
    </div>
  `;
  o.querySelector("#aLogin").onclick=()=>{
    const u=o.querySelector("#aUser").value,p=o.querySelector("#aPass").value,e=o.querySelector("#aErr");
    if(u===ADMIN_USER&&p===ADMIN_PASS){state.showAdminLogin=false;goTo("admin");}
    else{e.textContent="⚠ Invalid credentials.";e.style.display="block";}
  };
  o.querySelector("#aCancel").onclick=()=>{state.showAdminLogin=false;render();};
  return o;
}

// 2. SIGN IN
function renderSignIn(){
  const d=document.createElement("div");
  d.style.cssText="min-height:100vh;display:flex;align-items:center;justify-content:center;padding:20px;position:relative;z-index:1;";
  d.innerHTML=`
    <div class="card fade" style="padding:44px 38px;max-width:460px;width:100%;">
      <div style="text-align:center;margin-bottom:30px;">
        <div style="display:inline-flex;align-items:center;justify-content:center;width:58px;height:58px;border-radius:16px;background:linear-gradient(135deg,#2563eb,#7c3aed);margin-bottom:14px;font-size:24px;">📋</div>
        <h1 style="color:#1e293b;font-size:22px;font-weight:900;font-family:'Crimson Pro',serif;">ExamReady</h1>
        <p style="color:#94a3b8;margin-top:4px;font-size:13px;">Create your account to begin</p>
      </div>
      <div style="display:flex;flex-direction:column;gap:14px;">
        <div><label class="lbl">Full Name *</label><input id="siName" placeholder="Enter your full name" class="inp"/></div>
        <div><label class="lbl">Email Address *</label><input id="siEmail" type="email" placeholder="Enter your email" class="inp"/></div>
        <div><label class="lbl">Course *</label>
          <select id="siCourse" class="inp">
            <option value="">— Select your course —</option>
            <option value="B.Tech">B.Tech</option>
            <option value="Diploma">Diploma</option>
          </select>
        </div>
        <div><label class="lbl">Captcha Verification *</label>
          <div style="display:flex;align-items:center;gap:10px;">
            <div id="capQ" style="background:linear-gradient(135deg,#eff6ff,#f0f9ff);border:1.5px solid #bfdbfe;border-radius:10px;padding:10px 18px;color:#2563eb;font-weight:800;font-size:18px;letter-spacing:2px;flex-shrink:0;font-family:'JetBrains Mono',monospace;">${state.captcha.q}</div>
            <input id="capAns" placeholder="Answer" class="inp" style="width:90px;"/>
            <button id="capRefresh" class="btn-secondary" style="padding:10px 12px;font-size:16px;flex-shrink:0;">↻</button>
          </div>
        </div>
        <p id="siErr" style="color:#ef4444;font-size:13px;display:none;"></p>
        <button id="siSubmit" class="btn-primary" style="padding:13px;font-size:15px;margin-top:4px;">Proceed to Instructions →</button>
        <button id="siBack" class="btn-secondary">← Back to Home</button>
      </div>
    </div>
  `;
  d.querySelector("#capRefresh").onclick=()=>{state.captcha=makeCaptcha();d.querySelector("#capQ").textContent=state.captcha.q;d.querySelector("#capAns").value="";};
  d.querySelector("#siBack").onclick=()=>goTo("landing");
  d.querySelector("#siSubmit").onclick=()=>{
    const name=d.querySelector("#siName").value.trim(),email=d.querySelector("#siEmail").value.trim(),course=d.querySelector("#siCourse").value,cap=d.querySelector("#capAns").value.trim(),e=d.querySelector("#siErr");
    e.style.display="none";
    if(!name)return showErr(e,"Please enter your full name.");
    if(!email)return showErr(e,"Please enter your email address.");
    if(!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email))return showErr(e,"Please enter a valid email address.");
    if(!course)return showErr(e,"Please select your course.");
    if(!cap)return showErr(e,"Please solve the CAPTCHA.");
    if(parseInt(cap)!==state.captcha.ans){state.captcha=makeCaptcha();return showErr(e,"Incorrect CAPTCHA. Try again.");}
    if(state.questions.length===0)return showErr(e,"No questions available yet. Contact administrator.");
    state.student={name,email,course};state.instrRevealed=0;goTo("instructions");
  };
  return d;
}
function showErr(el,msg){el.textContent="⚠ "+msg;el.style.display="block";}

// 3. INSTRUCTIONS
function renderInstructions(){
  const d=document.createElement("div");
  d.style.cssText="min-height:100vh;background:#fff;display:flex;flex-direction:column;position:relative;z-index:1;";
  d.innerHTML=`
    <div class="topbar">
      <div style="display:flex;align-items:center;gap:10px;">
        <div style="width:34px;height:34px;border-radius:10px;background:linear-gradient(135deg,#2563eb,#7c3aed);display:flex;align-items:center;justify-content:center;font-size:15px;">📋</div>
        <div><div style="color:#1e293b;font-weight:800;font-size:14px;">ExamReady</div><div style="color:#94a3b8;font-size:11px;">Welcome, ${state.student.name}</div></div>
      </div>
    </div>
    <div style="flex:1;max-width:700px;margin:0 auto;width:100%;padding:32px 20px;overflow-y:auto;">
      <h2 style="color:#1e293b;font-size:22px;font-weight:900;margin-bottom:4px;font-family:'Crimson Pro',serif;">Exam Instructions</h2>
      <p style="color:#94a3b8;font-size:13px;margin-bottom:24px;">Please read all ins
