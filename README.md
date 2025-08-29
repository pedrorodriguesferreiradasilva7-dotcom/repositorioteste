
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>💖 Senha, Quiz e Pedido</title>
<style>
:root {--accent:#6b21a8; --secondary:#dc3545; --success:#28a745;}
* {box-sizing:border-box; font-family: Inter, sans-serif;}
html, body {margin:0; padding:0; height:100%; display:flex; align-items:center; justify-content:center; background: linear-gradient(135deg,#fdfbff 0%,#f6f8ff 100%); overflow:hidden;}
.card {background:#fff; padding:28px; border-radius:12px; box-shadow:0 10px 30px rgba(16,24,40,0.08); width:380px; max-width:94%; text-align:center; position:relative; z-index:10;}
h1 {margin:0 0 16px; font-size:22px; color:#0f172a;}
p {margin:0 0 20px; color:#475569;}
.pins {display:flex; gap:10px; justify-content:center; margin-bottom:14px;}
.pin {width:64px; height:64px; border-radius:10px; border:2px solid #e6e9ef; background:#fbfdff; display:flex; align-items:center; justify-content:center; font-size:32px; line-height:60px;}
.pin input {width:100%; height:100%; border:0; background:transparent; font-size:32px; text-align:center; outline:none;}
.buttons {display:flex; gap:10px; justify-content:center; margin-bottom:12px;}
button {padding:10px 14px; border-radius:10px; border:0; background:var(--accent); color:white; cursor:pointer; font-weight:600; transition: all 0.3s ease;}
.secondary {background:#eef2ff; color:var(--accent); border:1px solid rgba(107,33,168,0.08);}
.msg {height:22px; text-align:center; margin-top:12px; color:#ef4444; font-weight:700;}
.hint {font-size:13px; color:#64748b; text-align:center; margin-top:10px;}
#quizCard, #pedido, #errorMsg {display:none; text-align:center;}
#pedido {background:#fce7f3; padding:40px; border-radius:12px;}
#pedido h1 {font-size:24px; color:#d6336c;}
#pedido button {border-radius:50px; font-size:18px; padding:10px 20px;}
#errorMsg {background:#ffe4e1; color:#842029; font-size:20px; padding:30px; border-radius:12px;}
#errorMsg button {margin:10px auto; display:block;}
.heart {position:fixed; top:-30px; color:#ef476f; font-size:20px; animation:fall linear forwards; z-index:1; user-select:none; pointer-events:none;}
@keyframes fall {to{transform:translateY(110vh) rotate(360deg); opacity:0;}}
.shake {animation: shake 0.3s;}
@keyframes shake {0%{transform:translateX(0);}25%{transform:translateX(-5px);}50%{transform:translateX(5px);}75%{transform:translateX(-5px);}100%{transform:translateX(0);}}
</style>
</head>
<body>

<!-- Tela do cadeado -->
<main class="card" id="cadeado">
  <h1>💖 Senha do meu coração — digite 4 dígitos</h1>
  <p>Insira a sequência numérica de 4 dígitos para desbloquear.</p>
  <div class="pins">
    <div class="pin"><input maxlength="1" id="d0" inputmode="numeric"></div>
    <div class="pin"><input maxlength="1" id="d1" inputmode="numeric"></div>
    <div class="pin"><input maxlength="1" id="d2" inputmode="numeric"></div>
    <div class="pin"><input maxlength="1" id="d3" inputmode="numeric"></div>
  </div>
  <div class="buttons">
    <button id="check">Verificar</button>
    <button id="clear" class="secondary">Limpar</button>
  </div>
  <div class="hint">pergunta a senha pra a Isa.</div>
  <div class="msg" id="msg"></div>
</main>

<!-- Tela do Quiz -->
<main class="card" id="quizCard">
  <h1 id="quizQuestion"></h1>
  <div class="buttons">
    <button id="yesBtn">Sim</button>
    <button id="noBtn" class="secondary">Não</button>
  </div>
</main>

<!-- Tela do pedido -->
<main class="card" id="pedido">
  <h1>Quer namorar comigo? 🥺👉👈</h1>
  <div class="buttons">
    <button id="pedidoYes" style="background:#28a745;">Sim 💖</button>
    <button id="pedidoNo" style="background:#dc3545;">Não 💔</button>
  </div>
</main>

<div id="errorMsg"></div>

<script>
// ----------------- Cadeado -----------------
const SECRET = '0001';
const inputs = [0,1,2,3].map(i=>document.getElementById('d'+i));
const msg = document.getElementById('msg');
const checkBtn = document.getElementById('check');
const clearBtn = document.getElementById('clear');
const cadeado = document.getElementById('cadeado');
const quizCard = document.getElementById('quizCard');

inputs[0].focus();
inputs.forEach((inp, idx)=>{
  inp.addEventListener('input', e=>{
    e.target.value = e.target.value.replace(/[^0-9]/g,'').slice(-1);
    if(e.target.value && idx<3) inputs[idx+1].focus();
  });
  inp.addEventListener('keydown', e=>{
    if(e.key==='Backspace'&&!e.target.value&&idx>0) inputs[idx-1].focus();
    if(e.key==='ArrowLeft'&&idx>0) inputs[idx-1].focus();
    if(e.key==='ArrowRight'&&idx<3) inputs[idx+1].focus();
    if(e.key==='Enter') attempt();
  });
});

function getValue(){return inputs.map(i=>i.value||'').join('');}
function attempt(){
  const val = getValue();
  if(val.length < 4){
    msg.textContent = 'Preencha os 4 dígitos.';
    return;
  }
  if(val === SECRET){
    cadeado.style.display='none';
    showQuiz();
  } else {
    msg.textContent='Senha incorreta. Tente novamente.';
    inputs.forEach(i => i.classList.add('shake'));
    setTimeout(()=>inputs.forEach(i => i.classList.remove('shake')), 300);
    inputs[0].focus();
  }
}
checkBtn.addEventListener('click', attempt);
clearBtn.addEventListener('click', ()=>{inputs.forEach(i=>i.value=''); msg.textContent=''; inputs[0].focus();});

// ----------------- Quiz -----------------
const questions = [
  "Pergunta 1: Você é lésbica? 🌈",
  "Pergunta 2: Você é racista? ✊🏽",
  "Pergunta 3: Você é uma geladeira elotrux? 🧊",
  "Pergunta 4: Você me acha engraçado?😆"
];
let currentQuestion = 0;
const quizQuestion = document.getElementById('quizQuestion');
const yesBtn = document.getElementById('yesBtn');
const noBtn = document.getElementById('noBtn');
const pedido = document.getElementById('pedido');

function showQuiz(){
  quizCard.style.display='block';
  quizQuestion.textContent = questions[currentQuestion];
}
function nextQuestion(){
  currentQuestion++;
  if(currentQuestion < questions.length){
    quizQuestion.textContent = questions[currentQuestion];
  } else {
    quizCard.style.display='none';
    pedido.style.display='block';
  }
}
yesBtn.addEventListener('click', nextQuestion);
noBtn.addEventListener('click', nextQuestion);

// ----------------- Pedido -----------------
const pedidoYes = document.getElementById('pedidoYes');
const pedidoNo = document.getElementById('pedidoNo');
const errorMsg = document.getElementById('errorMsg');
const naoMessages = [
  "Será que você não quer clicar no Sim? 💖",
  "O Sim é muito legal! 😍",
  "Clique no Sim, por favor! 🌟",
  "Não resista ao Sim! 🥰"
];
let naoIndex = 0;

pedidoYes.addEventListener('click', ()=>{
  pedido.style.display='none';
  errorMsg.style.display='block';
  errorMsg.innerHTML = `
<div style="max-width:500px; margin:0 auto; padding:20px; background:#fff0f6; border-radius:15px; box-shadow:0 8px 20px rgba(0,0,0,0.1); font-family:Arial, sans-serif; color:#333; line-height:1.6;">
  <p>💌 <strong>Ana Namiê</strong>,</p>

  <p>Nem sei por onde começar, mas queria me expressar e te dizer que te amo muito. ❤️ Faz 4 meses que gosto de você, mas nunca tive coragem de falar sobre isso. Pode não ter parecido, mas foi difícil tentar conversar com você.</p>

  <p>Eu sei que às vezes ficava “Shippando” você com o Carlos, mas era a minha forma de tentar falar com você, porque você é muito difícil de conversar… mas ao mesmo tempo linda, simpática e legal. ✨ Espero que continue assim para sempre.</p>

  <p>Eu te amo de qualquer jeito, até nos piores dias, com espinhas ou dores. 💖 Espero que tenha gostado do site. Queria ter conversado mais ou te conhecido melhor, mas se você aceitar, provavelmente conseguirei, e quem sabe, como talvez seu namorado, possamos nos conhecer muito mais.</p>

  <p style="text-align:right;">ASS: <strong>Pedro</strong><br>29/08/2025</p>
</div>


    <button id="linkBtn" style="
      margin:20px auto;
      display:block;
      padding:12px 24px;
      border-radius:12px;
      border:none;
      background:linear-gradient(90deg,#ff3cac,#784ba0,#2b86c5);
      color:white;
      font-weight:700;
      font-size:18px;
      cursor:pointer;
      box-shadow:0 4px 15px rgba(0,0,0,0.2);
    ">Clique aqui</button>
    <button id="restartBtn" style="
      margin:10px auto;
      display:block;
      padding:10px 20px;
      border-radius:12px;
      border:none;
      background:#0d6efd;
      color:white;
      font-weight:600;
      cursor:pointer;
    ">Voltar ao início</button>
  `;
  document.getElementById('linkBtn').addEventListener('click', ()=>{ window.open('https://www.youtube.com/watch?v=lp-EO5I60KA','_blank'); });
  document.getElementById('restartBtn').addEventListener('click', ()=>{
    errorMsg.style.display='none';
    currentQuestion=0; naoIndex=0;
    inputs.forEach(i=>i.value=''); msg.textContent='';
    cadeado.style.display='block'; inputs[0].focus();
  });
});

pedidoNo.addEventListener('click', ()=>{
  pedidoNo.textContent = naoMessages[naoIndex];
  naoIndex++; if(naoIndex>=naoMessages.length) naoIndex=0;
});

// ----------------- Corações -----------------
function createHeart(){
  const heart=document.createElement('div');
  heart.className='heart';
  heart.textContent='❤';
  heart.style.left=Math.random()*100+'vw';
  heart.style.fontSize=(14+Math.random()*26)+'px';
  heart.style.animationDuration=(3+Math.random()*4)+'s';
  document.body.appendChild(heart);
  setTimeout(()=>heart.remove(),7000);
}
setInterval(createHeart,450);
</script>
</body>
</html>
