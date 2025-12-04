// Minimal ManuMaster web demo (vanilla JS + canvas)
// Controls: arrow buttons or keyboard (A/D or Left/Right), Space jump, J attack
const canvas = document.getElementById('game');
const ctx = canvas.getContext('2d');
let W = canvas.width, H = canvas.height;

let player = {x:120, y: H-140, w:48, h:64, vx:0, vy:0, onGround:false, lives:3};
let gravity = 1200;
let speedWalk = 180, speedRun=300;
let attackCooldown = 0;
let projectiles = [];
let gianni = {x:820, y: H-220, timer:0, baseRate:1.2, level:1, enraged:false};
let level = 1;
let timeLeft = 20 + level*5;
let lastTime = 0;
let keys = {left:false,right:false,jump:false,attack:false,run:false};

const hudLives = document.getElementById('lives');
const hudLevel = document.getElementById('level');

// Input bindings
document.addEventListener('keydown', e=>{
  if(e.key==='a' || e.key==='ArrowLeft') keys.left=true;
  if(e.key==='d' || e.key==='ArrowRight') keys.right=true;
  if(e.key==='Shift') keys.run=true;
  if(e.code==='Space') keys.jump=true;
  if(e.key==='j') keys.attack=true;
});
document.addEventListener('keyup', e=>{
  if(e.key==='a' || e.key==='ArrowLeft') keys.left=false;
  if(e.key==='d' || e.key==='ArrowRight') keys.right=false;
  if(e.key==='Shift') keys.run=false;
  if(e.code==='Space') keys.jump=false;
  if(e.key==='j') keys.attack=false;
});
// touch buttons
['left','right','jump','attack'].forEach(id=>{
  const b = document.getElementById(id);
  b.addEventListener('touchstart', e=>{ e.preventDefault(); if(id==='left') keys.left=true; if(id==='right') keys.right=true; if(id==='jump') keys.jump=true; if(id==='attack') keys.attack=true;});
  b.addEventListener('touchend', e=>{ e.preventDefault(); if(id==='left') keys.left=false; if(id==='right') keys.right=false; if(id==='jump') keys.jump=false; if(id==='attack') keys.attack=false;});
  b.addEventListener('mousedown', e=>{ e.preventDefault(); if(id==='left') keys.left=true; if(id==='right') keys.right=true; if(id==='jump') keys.jump=true; if(id==='attack') keys.attack=true;});
  b.addEventListener('mouseup', e=>{ e.preventDefault(); if(id==='left') keys.left=false; if(id==='right') keys.right=false; if(id==='jump') keys.jump=false; if(id==='attack') keys.attack=false;});
});

// utilities
function rand(min,max){ return Math.random()*(max-min)+min; }

function spawnProjectile(){
  const types = ['snake','pig','tongue','phone'];
  const t = types[Math.floor(Math.random()*types.length)];
  const dir = Math.random()<0.6 ? -1 : 1;
  const speed = 160 + level*20 + Math.random()*80;
  const p = {x: gianni.x + (dir<0?-40:40), y: gianni.y + 40 + Math.random()*40, vx: dir*speed, type: t, w:28, h:18};
  projectiles.push(p);
}

// Game loop
function update(dt){
  // player input
  let dir = 0;
  if(keys.left) dir -= 1;
  if(keys.right) dir += 1;
  let spd = keys.run ? speedRun : speedWalk;
  player.vx = dir*spd;
  // gravity
  player.vy += gravity*dt;
  player.x += player.vx*dt;
  player.y += player.vy*dt;
  // ground
  const groundY = H-80;
  if(player.y + player.h/2 >= groundY){ player.y = groundY - player.h/2; player.vy = 0; player.onGround=true; }
  else player.onGround=false;
  if(keys.jump && player.onGround){ player.vy = -520; player.onGround=false; }
  // attack
  attackCooldown = Math.max(0, attackCooldown - dt);
  if(keys.attack && attackCooldown<=0){
    attackCooldown = 0.5;
    // attack hits projectiles in front
    for(let i=projectiles.length-1;i>=0;i--){
      const p = projectiles[i];
      const inFront = Math.abs((p.x) - (player.x + (player.vx>=0?40:-40))) < 60;
      if(inFront && Math.abs(p.y - player.y) < 60){
        // destroy
        projectiles.splice(i,1);
      }
    }
  }
  // bounds
  if(player.x < 20) player.x = 20;
  if(player.x > W-20) player.x = W-20;

  // gianni spawning
  gianni.timer -= dt;
  let currentRate = gianni.baseRate - (level*0.08);
  if(level % 3 === 0) currentRate *= 0.6;
  currentRate = Math.max(0.18, currentRate);
  if(gianni.timer <= 0){
    spawnProjectile();
    gianni.timer = currentRate + Math.random()*0.6;
  }
  // move projectiles
  for(let i=projectiles.length-1;i>=0;i--){
    const p = projectiles[i];
    p.x += p.vx*dt;
    if(p.type==='snake') p.y = H-140;
    if(p.x < -100 || p.x > W+100) projectiles.splice(i,1);
    // collision with player
    if(Math.abs(p.x - player.x) < 34 && Math.abs(p.y - player.y) < 40){
      projectiles.splice(i,1);
      player.lives -= 1;
      hudLives.textContent = 'Vite: ' + player.lives;
      if(player.lives <= 0){ resetGame(); }
    }
  }

  // level timer
  timeLeft -= dt;
  if(timeLeft <= 0){
    levelUp();
  }
}

function levelUp(){
  level += 1;
  hudLevel.textContent = 'Livello: ' + level;
  timeLeft = 20 + level*5;
  showLevelUp(['aumento di soldi','assegno al merito','una tantum','livello professionale superiore','pacca sulla spalla'][Math.floor(Math.random()*5)]);
}

function showLevelUp(text){
  const el = document.createElement('div');
  el.style.position='absolute';
  el.style.left='50%';
  el.style.top='30%';
  el.style.transform='translate(-50%,-50%)';
  el.style.background='rgba(0,0,0,0.8)';
  el.style.padding='18px';
  el.style.borderRadius='8px';
  el.style.fontWeight='bold';
  el.textContent = 'Level Up! Hai ottenuto: ' + text;
  document.getElementById('game-wrap').appendChild(el);
  setTimeout(()=>el.remove(),1800);
}

function resetGame(){
  player.lives = 3;
  hudLives.textContent = 'Vite: ' + player.lives;
  projectiles = [];
  level = 1;
  hudLevel.textContent = 'Livello: ' + level;
  timeLeft = 20 + level*5;
}

// render
function draw(){
  ctx.clearRect(0,0,W,H);
  // background
  ctx.fillStyle = '#0b0b1a';
  ctx.fillRect(0,0,W,H);
  // factory boxes
  for(let i=0;i<6;i++){
    ctx.fillStyle = '#2a4a2a';
    ctx.fillRect(i*150+40, H-260, 80, 80);
  }
  // ground
  ctx.fillStyle = '#282828';
  ctx.fillRect(0, H-80, W, 80);
  // gianni
  ctx.fillStyle = '#d04f4f';
  ctx.fillRect(gianni.x-24, gianni.y-48, 48, 64);
  ctx.fillStyle = '#fff';
  ctx.fillText('Gianni', gianni.x-20, gianni.y+30);
  // player
  ctx.fillStyle = '#888';
  ctx.fillRect(player.x-24, player.y-64, player.w, player.h);
  ctx.fillStyle = '#0f0';
  ctx.fillRect(player.x-24+6, player.y-12, 12, 8); // green glove hint
  // projectiles
  projectiles.forEach(p=>{
    if(p.type==='pig') ctx.fillStyle='#f39';
    else if(p.type==='snake') ctx.fillStyle='#6a3';
    else ctx.fillStyle='#f1c40f';
    ctx.fillRect(p.x-12, p.y-8, p.w, p.h);
  });
}

function loop(ts){
  if(!lastTime) lastTime = ts;
  const dt = Math.min(0.05, (ts - lastTime)/1000);
  lastTime = ts;
  update(dt);
  draw();
  requestAnimationFrame(loop);
}
requestAnimationFrame(loop);
