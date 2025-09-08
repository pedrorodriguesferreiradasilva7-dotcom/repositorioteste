<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Jornada Interativa 💖</title>
<style>
  body{margin:0;font-family:sans-serif;overflow:hidden;background:linear-gradient(180deg,#0f172a,#071133);color:white;-webkit-tap-highlight-color:transparent;}
  .heart{position:fixed;top:-30px;color:#ef476f;font-size:20px;animation:fall linear forwards;user-select:none;pointer-events:none;}
  @keyframes fall{to{transform:translateY(110vh) rotate(360deg);opacity:0}}
  section{position:absolute;inset:0;display:flex;flex-direction:column;align-items:center;justify-content:center;}
  .hidden{display:none;}
  h1{text-align:center;margin-bottom:20px;font-size:1.8rem;}
  /* Slider */
  .rail{position:relative;width:80%;max-width:300px;height:60px;border-radius:40px;background:rgba(255,255,255,0.1);display:flex;align-items:center;overflow:hidden;margin-top:50px;}
  .thumb{width:52px;height:52px;border-radius:50%;background:#fff;color:#000;display:flex;align-items:center;justify-content:center;cursor:grab;position:relative;z-index:2;touch-action:none;}
  .progress{position:absolute;top:0;left:0;bottom:0;background:#ff4d85;width:0;border-radius:40px;z-index:1;}
  /* Memória */
  .grid{display:grid;grid-template-columns:repeat(4,1fr);gap:10px;width:90%;max-width:360px;}
  .card{padding-top:100%;position:relative;background:#fff;border-radius:10px;display:flex;align-items:center;justify-content:center;font-size:2rem;cursor:pointer;box-shadow:0 2px 6px rgba(0,0,0,0.2);}
  .card span{position:absolute;top:0;left:0;right:0;bottom:0;display:flex;align-items:center;justify-content:center;}
  .matched{background:#ffb3c6;cursor:default;}
  /* Labirinto */
  canvas{background:#fff;border:2px solid #333;margin-top:20px;touch-action:none;}
  /* Final */
  .final{background:#ffeff5;color:#333;padding:40px;border-radius:20px;text-align:center;font-size:1.2rem;}
</style>
</head>
<body>

<!-- Tela Inicial -->
<section id="inicio">
  <h1>Arraste a bolinha para começar 💖</h1>
  <div class="rail" id="rail">
    <div class="progress" id="progress"></div>
    <div class="thumb" id="thumb">→</div>
  </div>
</section>

<!-- Jogo da Memória -->
<section id="memoria" class="hidden">
  <h1>Encontre os pares 💖</h1>
  <div class="grid" id="grid"></div>
</section>

<!-- Labirinto -->
<section id="labirinto" class="hidden">
  <h1>Encontre o coração no labirinto 💘</h1>
  <canvas id="game" width="400" height="400"></canvas>
</section>

<!-- Mensagem Final -->
<section id="final" class="hidden">
  <div class="final">
    <h1>Parabéns! 💖</h1>
    <p>Você completou todos os jogos e chegou ao coração final!</p>
  </div>
</section>

<script>
// ===== Corações caindo =====
function createHeart(){
  const h=document.createElement('div');
  h.className='heart';h.textContent='❤';
  h.style.left=Math.random()*100+'vw';
  h.style.fontSize=(14+Math.random()*26)+'px';
  h.style.animationDuration=(3+Math.random()*4)+'s';
  document.body.appendChild(h);
  setTimeout(()=>h.remove(),7000);
}
setInterval(createHeart,450);

// ===== Slider Inicial =====
const rail=document.getElementById('rail');
const thumb=document.getElementById('thumb');
const progress=document.getElementById('progress');
const inicio=document.getElementById('inicio');
let dragging=false, startX=0, thumbX=0;

function updateThumb(pos){
  const max=rail.offsetWidth - thumb.offsetWidth;
  const pct=Math.max(0,Math.min(1,pos/max));
  progress.style.width=(pct*100)+'%';
  thumb.style.transform=`translateX(${pct*max}px)`;
  if(pct>0.7){inicio.classList.add('hidden'); startMemoria();}
}

thumb.addEventListener('touchstart',e=>{dragging=true; startX=e.touches[0].clientX; thumbX=parseInt(thumb.style.transform.replace('translateX(','')||0);});
thumb.addEventListener('touchmove',e=>{if(!dragging)return; updateThumb(thumbX + (e.touches[0].clientX-startX));});
thumb.addEventListener('touchend',()=>{dragging=false; if(parseInt(progress.style.width)<70) updateThumb(0);});
thumb.addEventListener('mousedown',e=>{dragging=true; startX=e.clientX; thumbX=parseInt(thumb.style.transform.replace('translateX(','')||0);});
window.addEventListener('mousemove',e=>{if(!dragging)return; updateThumb(thumbX + (e.clientX-startX));});
window.addEventListener('mouseup',()=>{dragging=false; if(parseInt(progress.style.width)<70) updateThumb(0);});

// ===== Jogo da Memória =====
function startMemoria(){
  const memoria=document.getElementById('memoria');
  memoria.classList.remove('hidden');
  const emojis=['💖','🌟','🍀','🐱','🐶','🎵','⚡','🍎'];
  let cards=[...emojis,...emojis].sort(()=>Math.random()-0.5);
  const grid=document.getElementById('grid');
  grid.innerHTML='';
  let first=null,lock=false,matched=0;

  cards.forEach(sym=>{
    const c=document.createElement('div');
    c.className='card';
    const span=document.createElement('span');
    span.textContent='';
    c.appendChild(span);
    c.dataset.sym=sym;

    function flipCard(){
      if(lock || c.classList.contains('matched') || span.textContent) return;
      span.textContent = sym;
      c.style.background='#ffe4e1';

      if(!first){
        first={card:c,span:span};
      } else {
        if(first.card.dataset.sym===c.dataset.sym){
          first.card.classList.add('matched'); 
          c.classList.add('matched');
          matched+=2;
          first=null;
          if(matched===cards.length){
            setTimeout(()=>{
              memoria.classList.add('hidden'); 
              startLabirinto();
            },500);
          }
        } else {
          lock=true;
          setTimeout(()=>{
            first.span.textContent=''; first.card.style.background='#fff';
            span.textContent=''; c.style.background='#fff';
            first=null; lock=false;
          },600);
        }
      }
    }

    c.addEventListener('click', flipCard);
    c.addEventListener('touchstart', flipCard);
    grid.appendChild(c);
  });
}

// ===== Labirinto =====
function startLabirinto(){
  const lab=document.getElementById('labirinto');
  lab.classList.remove('hidden');
  const canvas=document.getElementById('game');
  const ctx=canvas.getContext('2d');
  const size=40, rows=10, cols=10;
  const player={x:0,y:0}, goal={x:9,y:9};
  const maze=[
    [0,1,0,0,0,1,0,0,0,0],
    [0,1,0,1,0,1,0,1,1,0],
    [0,0,0,1,0,0,0,1,0,0],
    [1,1,0,0,1,1,0,1,0,1],
    [0,0,0,1,0,0,0,0,0,0],
    [0,1,1,1,0,1,1,1,1,0],
    [0,0,0,0,0,0,0,1,0,0],
    [1,1,0,1,1,1,0,1,1,0],
    [0,0,0,0,0,0,0,0,0,0],
    [0,1,1,1,1,1,1,1,1,0],
  ];
  function draw(){
    ctx.clearRect(0,0,canvas.width,canvas.height);
    for(let y=0;y<rows;y++){
      for(let x=0;x<cols;x++){
        if(maze[y][x]===1){ctx.fillStyle='#333';ctx.fillRect(x*size,y*size,size,size);}
      }
    }
    ctx.fillStyle='blue';ctx.fillRect(player.x*size,player.y*size,size,size);
    ctx.fillStyle='red';ctx.font='30px sans-serif';ctx.fillText('❤',goal.x*size+8,goal.y*size+28);
  }
  draw();
  window.onkeydown=e=>{
    let nx=player.x,ny=player.y;
    if(e.key==='ArrowUp')ny--;if(e.key==='ArrowDown')ny++;
    if(e.key==='ArrowLeft')nx--;if(e.key==='ArrowRight')nx++;
    if(nx>=0&&ny>=0&&nx<cols&&ny<rows&&maze[ny][nx]===0){
      player.x=nx;player.y=ny;draw();
      if(player.x===goal.x&&player.y===goal.y){
        setTimeout(()=>{lab.classList.add('hidden');document.getElementById('final').classList.remove('hidden');},200);
      }
    }
  }
}
</script>
</body>
</html>
