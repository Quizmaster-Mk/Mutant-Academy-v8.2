<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Mutant Academy — V4.2 (clean)</title>
<style>
  :root{
    --bg1:#041021; --panel:#0b2230; --muted:#9fb0bd; --accent:#6ee7b7; --danger:#ff6b6b;
    --accent2:#7cc1ff; --gold:#ffd166; --text:#e9fbf6;
  }
  *{box-sizing:border-box}
  html,body{height:100%;margin:0;font-family:Inter,system-ui,Arial;background:linear-gradient(180deg,var(--bg1),#00111a);color:var(--text);display:flex;align-items:flex-start;justify-content:center;padding:18px}
  .app{width:100%;max-width:820px;background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.006));border-radius:14px;padding:14px;border:1px solid rgba(255,255,255,0.04);box-shadow:0 20px 60px rgba(0,0,0,0.6)}
  h1{margin:6px 0 4px 0}
  .muted{color:var(--muted);font-size:13px}
  .panel{background:linear-gradient(180deg, rgba(255,255,255,0.01), rgba(255,255,255,0.006));padding:12px;border-radius:10px;border:1px solid rgba(255,255,255,0.03);margin-top:10px}
  .row{display:flex;gap:8px;align-items:center}
  .col{flex:1}
  button{background:#071827;border:1px solid rgba(255,255,255,0.04);color:var(--text);padding:10px;border-radius:8px;cursor:pointer;font-weight:700}
  button:disabled{opacity:.45;cursor:not-allowed}
  .grid{display:grid;grid-template-columns:1fr 1fr;gap:8px;margin-top:8px}
  .avatar{width:84px;height:84px;border-radius:10px;background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));display:flex;align-items:center;justify-content:center;font-size:44px}
  .hpbar{height:14px;background:rgba(255,255,255,0.02);border-radius:10px;overflow:hidden;border:1px solid rgba(255,255,255,0.03);margin-top:8px}
  .fill{height:100%;transition:width .6s cubic-bezier(.2,.9,.2,1);background:linear-gradient(90deg,var(--accent),#15b89a)}
  .fill.enemy{background:linear-gradient(90deg,#ff7b7b,#ff4d4d)}
  .log{height:200px;overflow:auto;padding:8px;border-radius:8px;background:rgba(0,0,0,0.12);margin-top:10px;font-size:13px;color:var(--muted)}
  .actions{display:flex;gap:8px;margin-top:10px}
  .action{flex:1;padding:10px;border-radius:8px;background:#07202a;border:1px solid rgba(255,255,255,0.03);cursor:pointer;font-weight:800}
  .floating{position:fixed;pointer-events:none;font-weight:900;text-shadow:0 6px 14px rgba(0,0,0,0.6);animation:floatUp 900ms ease-out forwards;z-index:9999}
  @keyframes floatUp{from{opacity:1;transform:translate(-50%,0) scale(1)}to{opacity:0;transform:translate(-50%,-80px) scale(1.05)}}
  .attackAnim{position:fixed;padding:8px 12px;border-radius:10px;background:rgba(255,255,255,0.02);font-weight:900;z-index:9998;transition:transform .42s cubic-bezier(.2,.9,.2,1),opacity .42s}
  .glow{box-shadow:0 0 22px rgba(255,200,120,0.16);transition:box-shadow .24s}
  .shake{animation:shakeAnim .35s}
  @keyframes shakeAnim{
    0%{transform:translateY(0)}20%{transform:translateY(-6px)}40%{transform:translateY(4px)}60%{transform:translateY(-3px)}80%{transform:translateY(2px)}100%{transform:translateY(0)}
  }
  .screen { opacity:0; transform:translateY(8px); transition:opacity .32s ease, transform .32s ease; display:none }
  .screen.show { display:block; opacity:1; transform:translateY(0) }
  .particle { position:fixed; width:8px; height:8px; border-radius:50%; pointer-events:none; opacity:0.9; z-index:1200; animation:particleFloat 600ms linear forwards; }
  @keyframes particleFloat { from { transform: translateY(0) scale(1); opacity:1 } to { transform: translateY(-80px) translateX(30px) scale(.6); opacity:0 } }
  .center{display:flex;align-items:center;justify-content:center}
  .badge{padding:6px 10px;border-radius:999px;background:rgba(255,255,255,0.02);border:1px solid rgba(255,255,255,0.03);font-size:13px}
  @media(max-width:820px){ .grid{grid-template-columns:1fr} .row{flex-direction:column;align-items:stretch} }
</style>
</head>
<body>
<div class="app" role="application" aria-label="Mutant Academy V4.2">
  <header class="row">
    <div>
      <h1>Mutant Academy — Danger Room (V4.2)</h1>
      <div class="muted">Base stable — pouvoirs injectés uniquement par JS (plus de doublons).</div>
    </div>
    <div class="col center"><span class="badge" id="uiStage">Prêt</span></div>
  </header>

  <!-- WELCOME -->
  <section id="screenWelcome" class="panel screen show">
    <h2>Bienvenue, aspirant mutant</h2>
    <p class="muted">Tu vas traverser 3 rencontres : 2 entraînements puis le boss final.</p>
    <div style="margin-top:8px" class="row">
      <button id="btnStartToDiff">Commencer</button>
    </div>
  </section>

  <!-- DIFFICULTY -->
  <section id="screenDiff" class="panel screen">
    <h3>Choisis la difficulté</h3>
    <div class="grid" style="margin-top:8px">
      <button class="diffBtn" data-diff="facile">😃 Facile<br><span class="muted">PV ↑, soins ↑</span></button>
      <button class="diffBtn" data-diff="normal">😐 Normal<br><span class="muted">Équilibré</span></button>
      <button class="diffBtn" data-diff="difficile">😈 Difficile<br><span class="muted">PV ↓, ennemis ↑</span></button>
      <div style="grid-column:1/-1" class="muted">Sélectionne une difficulté, puis clique <b>Suivant</b>.</div>
    </div>
    <div style="margin-top:10px" class="row">
      <button id="btnDiffNext" disabled>Suivant →</button>
      <button id="btnDiffBack">← Retour</button>
    </div>
  </section>

  <!-- POWER (empty grid — JS injects) -->
  <section id="screenPower" class="panel screen">
    <h3>Choisis ton pouvoir (2 usages)</h3>
    <div class="grid" id="powerGrid"></div>
    <div style="margin-top:10px" class="row">
      <button id="btnPowerConfirm" disabled>▶️ Démarrer l'entraînement</button>
      <button id="btnPowerBack">← Retour</button>
    </div>
  </section>

  <!-- GAME -->
  <section id="screenGame" class="panel screen">
    <div class="row" style="align-items:flex-start">
      <div style="flex:1">
        <div style="display:flex;gap:12px;align-items:center">
          <div class="avatar" id="playerAvatar">🦸</div>
          <div style="flex:1">
            <div style="display:flex;justify-content:space-between;align-items:center">
              <div><strong id="playerName">Élève</strong></div>
              <div class="muted">PV: <span id="playerPV">0</span>/<span id="playerMax">0</span></div>
            </div>
            <div class="hpbar"><div id="playerFill" class="fill" style="width:100%"></div></div>
            <div class="muted" id="playerNote" style="margin-top:6px">Pouvoir: —</div>
          </div>
        </div>

        <div style="margin-top:10px">
          <div class="muted">Inventaire</div>
          <div id="inventory" style="display:flex;gap:8px;flex-wrap:wrap;margin-top:8px"></div>
        </div>

        <div id="afterWinChoices" class="panel" hidden>
          <div class="muted">Après victoire — choisis :</div>
          <div class="row" style="margin-top:8px">
            <button onclick="chooseAfterWin('fight')">⚔️ Affronter</button>
            <button onclick="chooseAfterWin('heal')">💊 Soins (+PV)</button>
            <button onclick="chooseAfterWin('bonus')">✨ Bonus (objet)</button>
          </div>
        </div>

        <div id="actions" style="margin-top:12px"></div>

        <div class="muted" style="margin-top:10px">Historique</div>
        <div id="log" class="log"></div>
      </div>

      <aside style="width:320px">
        <div class="panel">
          <div style="display:flex;gap:12px;align-items:center">
            <div class="avatar" id="enemyAvatar">—</div>
            <div style="flex:1">
              <div style="display:flex;justify-content:space-between;align-items:center">
                <div><strong id="enemyName">Aucun</strong></div>
                <div class="muted">PV: <span id="enemyPV">—</span>/<span id="enemyMax">—</span></div>
              </div>
              <div class="hpbar" style="margin-top:8px"><div id="enemyFill" class="fill enemy" style="width:0%"></div></div>
              <div class="muted" id="enemyNote" style="margin-top:6px">—</div>
            </div>
          </div>

          <div style="margin-top:12px" class="muted">Statut</div>
          <div style="margin-top:8px;display:flex;gap:8px;justify-content:space-between">
            <div class="badge">Score: <span id="score">0</span></div>
            <div class="badge">Usages pouvoir: <span id="usesLeft">0</span></div>
          </div>
        </div>

        <div style="margin-top:12px" class="panel">
          <div class="muted">Raccourcis</div>
          <div style="margin-top:8px;display:flex;gap:8px">
            <button id="btnUsePower">✨ Utiliser pouvoir</button>
            <button id="btnStatus">📋 Statut</button>
          </div>
          <div class="muted" style="margin-top:8px">Ton pouvoir est utilisable 2 fois sur toute la partie.</div>
        </div>
      </aside>
    </div>
  </section>

  <!-- END -->
  <section id="screenEnd" class="panel screen">
    <h2 id="endTitle">—</h2>
    <p id="endText" class="muted"></p>
    <div style="margin-top:10px">
      <button id="btnReplay">🔁 Rejouer</button>
    </div>
  </section>
</div>

<script>
/* V4.2 - Clean, no duplicate powers. Animations kept. */

/* ---------- Data ---------- */
const POWERS = [
  {id:'telekinesis',name:'Télékinésie',emoji:'🧠',desc:'Assomme 1 tour.',uses:2},
  {id:'claws',name:'Griffes',emoji:'🗡️',desc:'+8 dégâts sur prochaine attaque',uses:2},
  {id:'speed',name:'Vitesse',emoji:'⚡',desc:'Action supplémentaire',uses:2},
  {id:'ice',name:'Glace',emoji:'❄️',desc:'Gèle 1 tour',uses:2},
  {id:'pyro',name:'Pyro',emoji:'🔥',desc:'Brûlure 2 tours',uses:2},
  {id:'shield',name:'Champ',emoji:'🛡️',desc:'Réduit prochain dégât de moitié',uses:2}
];

const ENCOUNTERS = {
  training1: {id:'training1',name:"Robot d'entraînement",emoji:'🤖',pv:18,dmg:[2,4],note:'IA légère'},
  training2: {id:'training2',name:"Drone avancé",emoji:'🛸',pv:24,dmg:[3,5],note:'Tirs précis'},
  boss: {id:'boss',name:"Magneto (Boss)",emoji:'🧲',pv:46,dmg:[5,9],note:'Champ magnétique puissant'}
};

/* ---------- State ---------- */
let STATE = {
  difficulty:'normal',
  chosenPower:null,
  powerUsesLeft:0,
  player:{name:'Élève',emoji:'🦸',pv:40,max:40,shield:false,queuedClaws:0,boost:0,extraAction:false,evade:0},
  enemy:null,
  enemyStatus:{stunned:0,burn:0},
  score:0,
  encounterIndex:0,
  inCombat:false
};

/* ---------- DOM helpers ---------- */
const $ = id => document.getElementById(id);
const showScreen = (id) => {
  document.querySelectorAll('.screen').forEach(s=>s.classList.remove('show'));
  $(id).classList.add('show');
};
function log(msg){
  const d = document.createElement('div'); d.innerHTML = msg;
  $('log').prepend(d);
}
function spawnFloatingOver(el, txt, color='#ffcccc'){
  if(!el) return;
  const r = el.getBoundingClientRect();
  const f = document.createElement('div');
  f.className = 'floating';
  f.textContent = txt;
  f.style.left = (r.left + r.width/2) + 'px';
  f.style.top = (r.top + 8) + 'px';
  f.style.color = color;
  document.body.appendChild(f);
  setTimeout(()=> f.remove(), 900);
}
function animateAttack(fromEl, toEl, icon, cb){
  if(!fromEl || !toEl){ if(cb) cb(); return; }
  const r1 = fromEl.getBoundingClientRect(), r2 = toEl.getBoundingClientRect();
  const anim = document.createElement('div');
  anim.className = 'attackAnim';
  anim.textContent = icon;
  anim.style.left = (r1.left + r1.width/2) + 'px';
  anim.style.top = (r1.top + r1.height/2) + 'px';
  document.body.appendChild(anim);
  requestAnimationFrame(()=>{ anim.style.transform = `translate(${r2.left + r2.width/2 - (r1.left + r1.width/2)}px, ${r2.top + r2.height/2 - (r1.top + r1.height/2)}px)`; anim.style.opacity=0.96; });
  setTimeout(()=>{ anim.remove(); if(cb) cb(); }, 420);
}
function screenShake(){ const app = document.querySelector('.app'); app.classList.add('shake'); setTimeout(()=> app.classList.remove('shake'), 360); }
function spawnParticles(x,y,color='#ff9a3d',count=8){ for(let i=0;i<count;i++){ const p = document.createElement('div'); p.className='particle'; p.style.left = x + (Math.random()*40 - 20) + 'px'; p.style.top = y + (Math.random()*20 - 10) + 'px'; p.style.background = color; document.body.appendChild(p); setTimeout(()=> p.remove(), 700); } }
function rand(min,max){ return Math.floor(Math.random()*(max-min+1))+min; }

/* ---------- Init UI ---------- */
function init(){
  // Start -> difficulty
  $('btnStartToDiff').addEventListener('click', ()=>{ showScreen('screenDiff'); $('uiStage').textContent='Choix difficulté'; });

  // bind diff buttons
  const diffBtns = document.querySelectorAll('.diffBtn');
  diffBtns.forEach(btn=>{
    btn.style.opacity = 1;
    btn.addEventListener('click', ()=>{
      diffBtns.forEach(b=>b.style.opacity=0.6);
      btn.style.opacity = 1;
      $('btnDiffNext').disabled = false;
      STATE.difficulty = btn.dataset.diff;
      log(`⚙️ Difficulté: ${STATE.difficulty}`);
    });
  });
  $('btnDiffNext').addEventListener('click', ()=> { if(!STATE.difficulty){ alert('Choisis une difficulté'); return; } showScreen('screenPower'); $('uiStage').textContent='Choix pouvoir'; });
  $('btnDiffBack').addEventListener('click', ()=> { showScreen('screenWelcome'); $('uiStage').textContent='Prêt'; });

  // render power grid (only once)
  const grid = $('powerGrid');
  POWERS.forEach(p=>{
    const el = document.createElement('div'); el.className='panel'; el.style.cursor='pointer';
    el.innerHTML = `<div style="display:flex;gap:8px;align-items:center"><div style="font-size:28px">${p.emoji}</div>
      <div style="flex:1"><div style="font-weight:800">${p.name}</div><div class="muted" style="margin-top:6px">${p.desc}</div></div></div>`;
    el.addEventListener('click', ()=> selectPower(p.id, el));
    grid.appendChild(el);
  });

  // power navigation
  $('btnPowerBack').addEventListener('click', ()=> { showScreen('screenDiff'); $('uiStage').textContent='Choix difficulté'; });
  $('btnPowerConfirm').addEventListener('click', ()=> { if(!STATE.chosenPower) return alert('Choisis un pouvoir'); startGame(); });

  // shortcuts
  $('btnUsePower').addEventListener('click', ()=> usePower());
  $('btnStatus').addEventListener('click', ()=> alertStatus());
  $('btnReplay').addEventListener('click', ()=> location.reload());

  // show welcome
  showScreen('screenWelcome');
}
init();

/* ---------- Power selection ---------- */
function selectPower(id, elem){
  const p = POWERS.find(x=>x.id===id);
  STATE.chosenPower = p;
  STATE.powerUsesLeft = p.uses;
  STATE.player.emoji = p.emoji;
  document.querySelectorAll('#powerGrid .panel').forEach(n=>n.style.opacity=0.6);
  elem.style.opacity = 1;
  $('btnPowerConfirm').disabled = false;
  log(`✨ Pouvoir choisi : ${p.name}`);
}

/* ---------- Start game ---------- */
function startGame(){
  STATE.player.max = (STATE.difficulty === 'facile')?48: (STATE.difficulty==='difficile')?36:40;
  STATE.player.pv = STATE.player.max;
  STATE.score = 0; STATE.encounterIndex = 0; STATE.inCombat = false;
  $('uiStage').textContent = 'Entraînement';
  showScreen('screenGame');
  prepareEncounter(0);
  updateAll();
}

/* ---------- Prepare encounter ---------- */
function prepareEncounter(index){
  STATE.encounterIndex = index;
  const temp = index===0 ? ENCOUNTERS.training1 : index===1 ? ENCOUNTERS.training2 : ENCOUNTERS.boss;
  STATE.enemy = {...temp, pv: temp.pv, max: temp.pv};
  STATE.enemyStatus = {stunned:0,burn:0};
  STATE.inCombat = true;
  $('afterWinChoices').hidden = true;
  log(`🚪 Rencontre : ${STATE.enemy.name}`);
  updateAll();
  renderCombatActions();
}

/* ---------- Update UI ---------- */
function updateAll(){
  $('playerAvatar').textContent = STATE.player.emoji || '🦸';
  $('playerPV').textContent = STATE.player.pv;
  $('playerMax').textContent = STATE.player.max;
  $('playerFill').style.width = Math.max(0, (STATE.player.pv / STATE.player.max * 100)) + '%';
  $('playerNote').textContent = STATE.chosenPower ? `${STATE.chosenPower.emoji} ${STATE.chosenPower.name} — usages: ${STATE.powerUsesLeft}` : 'Pouvoir: —';

  if(STATE.enemy){
    $('enemyAvatar').textContent = STATE.enemy.emoji;
    $('enemyName').textContent = STATE.enemy.name;
    $('enemyPV').textContent = STATE.enemy.pv;
    $('enemyMax').textContent = STATE.enemy.max;
    $('enemyFill').style.width = Math.max(0, (STATE.enemy.pv / STATE.enemy.max * 100)) + '%';
    $('enemyNote').textContent = STATE.enemy.note || '';
  }
  $('score').textContent = STATE.score;
  $('usesLeft').textContent = STATE.powerUsesLeft;
}

/* ---------- Combat rendering ---------- */
function renderCombatActions(){
  const container = $('actions'); container.innerHTML = '';
  const a1 = document.createElement('button'); a1.className='action'; a1.textContent='👊 Attaque légère (2-4)'; a1.addEventListener('click', ()=> playerAttack('light'));
  const a2 = document.createElement('button'); a2.className='action'; a2.textContent='💥 Coup fort (4-8)'; a2.addEventListener('click', ()=> playerAttack('heavy'));
  const a3 = document.createElement('button'); a3.className='action'; a3.textContent='🛡️ Défendre'; a3.addEventListener('click', ()=> playerDefend());
  container.appendChild(a1); container.appendChild(a2); container.appendChild(a3);
}

/* ---------- Player actions ---------- */
function playerAttack(kind){
  if(!STATE.inCombat || !STATE.enemy) return;
  let dmg = (kind==='light')? rand(2,4) : rand(4,8);
  if(STATE.player.queuedClaws > 0){ dmg += STATE.player.queuedClaws; STATE.player.queuedClaws = 0; log('🗡️ Bonus Griffes appliqué.'); }
  if(STATE.player.boost > 0){ dmg += 5; STATE.player.boost--; log('⚡ Booster actif : +5 dégâts'); }
  if(Math.random() < 0.18){ dmg *= 2; log('✨ Coup critique !'); spawnFloatingOver($('enemyFill'), '✨CRIT✨', '#fff3a6'); }

  animateAttack($('playerAvatar'), $('enemyFill'), STATE.player.emoji, ()=>{
    STATE.enemy.pv -= dmg;
    spawnFloatingOver($('enemyFill'), '-'+dmg, '#ffb3b3');
    updateAll();
    setTimeout(()=> postPlayerEffects(), 360);
  });
}

function playerDefend(){
  STATE.player.evade = 0.35;
  log('🛡️ Position défensive : prochain dégât réduit.');
  setTimeout(()=> enemyTurn(), 420);
}

/* ---------- Use power ---------- */
function usePower(){
  if(!STATE.chosenPower) return alert('Aucun pouvoir choisi.');
  if(STATE.powerUsesLeft <= 0) return alert('Plus d\'usages du pouvoir.');
  if(!STATE.inCombat) return alert('Utilise le pouvoir en combat.');
  const p = STATE.chosenPower; STATE.powerUsesLeft--; updateAll();
  log(`✨ Tu actives ${p.name}`);
  if(p.id==='telekinesis'){ STATE.enemyStatus.stunned = 1; log('🧠 Assommé 1 tour'); }
  else if(p.id==='claws'){ STATE.player.queuedClaws += 8; log('🗡️ +8 dégâts sur prochaine attaque'); }
  else if(p.id==='speed'){ STATE.player.extraAction = true; log('⚡ Action supplémentaire disponible'); }
  else if(p.id==='ice'){ STATE.enemyStatus.stunned = 1; log('❄️ Gel — ennemi rate son prochain tour'); spawnParticlesOverEnemy('#9be7ff'); }
  else if(p.id==='pyro'){ STATE.enemyStatus.burn = 2; log('🔥 Brûlure appliquée (2 tours)'); spawnParticlesOverEnemy('#ffb86b'); }
  else if(p.id==='shield'){ STATE.player.shield = true; log('🛡️ Bouclier activé'); }
  updateAll();
  if(!STATE.player.extraAction) setTimeout(()=> enemyTurn(), 420);
  else STATE.player.extraAction = false;
}
function spawnParticlesOverEnemy(color){
  const el = $('enemyAvatar'); if(!el) return;
  const r = el.getBoundingClientRect(); spawnParticles(r.left + r.width/2, r.top + r.height/2, color, 10);
}

/* ---------- Post player effects ---------- */
function postPlayerEffects(){
  if(STATE.enemyStatus.burn && STATE.enemyStatus.burn > 0){
    const b = 3; STATE.enemy.pv -= b; STATE.enemyStatus.burn--; spawnFloatingOver($('enemyFill'), '-'+b, '#ffb86b'); log(`🔥 Brûlure : ${b} dégâts`); updateAll();
  }
  if(STATE.enemy.pv <= 0) return winEncounter();
  if(STATE.enemyStatus.stunned && STATE.enemyStatus.stunned > 0){
    STATE.enemyStatus.stunned--; log(`💤 ${STATE.enemy.name} est étourdi et rate son tour.`); updateAll();
  } else {
    setTimeout(()=> enemyTurn(), 420);
  }
}

/* ---------- Enemy turn ---------- */
function enemyTurn(){
  if(!STATE.inCombat || !STATE.enemy) return;
  if(STATE.enemyStatus.stunned && STATE.enemyStatus.stunned > 0){ STATE.enemyStatus.stunned--; log(`💤 ${STATE.enemy.name} reste étourdi.`); updateAll(); return; }
  const dmg = rand(STATE.enemy.dmg[0], STATE.enemy.dmg[1]);
  let final = dmg;
  if(STATE.player.shield){ final = Math.ceil(final/2); STATE.player.shield = false; log('🛡️ Bouclier : dégât réduit de moitié.'); }
  if(STATE.player.evade && STATE.player.evade > 0){ const red = Math.round(final * STATE.player.evade); final = Math.max(0, final - red); STATE.player.evade = 0; log(`↩️ Esquive : -${red}`); }
  STATE.player.pv -= final;
  spawnFloatingOver($('playerFill'), '-'+final, '#ff9aa2');
  screenShake(); flashElement($('playerAvatar'));
  log(`💥 ${STATE.enemy.name} inflige ${final} PV`);
  updateAll();
  if(STATE.player.pv <= 0) loseGame();
}
function flashElement(el){ if(!el) return; const orig = el.style.boxShadow; el.style.boxShadow = '0 0 18px rgba(255,100,100,0.9)'; setTimeout(()=> el.style.boxShadow = orig, 260); }

/* ---------- Win / Lose ---------- */
function winEncounter(){
  STATE.inCombat = false; STATE.score += 15; updateAll(); log(`✅ ${STATE.enemy.name} vaincu !`);
  if(STATE.encounterIndex >= 2){ finishGame(true); return; }
  $('afterWinChoices').hidden = false;
}

/* ---------- After-win choices ---------- */
function chooseAfterWin(choice){
  $('afterWinChoices').hidden = true;
  if(choice === 'fight'){ log('⚔️ Tu choisis d\'affronter la salle suivante.'); }
  else if(choice === 'heal'){ const heal = STATE.difficulty==='facile'?12:STATE.difficulty==='difficile'?5:8; const prev = STATE.player.pv; STATE.player.pv = Math.min(STATE.player.max, STATE.player.pv + heal); log(`💚 +${STATE.player.pv - prev} PV`); }
  else if(choice === 'bonus'){ if(Math.random() < 0.6){ const prev = STATE.player.pv; STATE.player.pv = Math.min(STATE.player.max, STATE.player.pv + 10); log(`🎁 Sérum : +${STATE.player.pv - prev} PV`); } else { STATE.player.boost = 2; log('⚡ Booster trouvé : +5 dégâts pendant 2 attaques'); } }
  setTimeout(()=> { STATE.encounterIndex++; prepareEncounter(STATE.encounterIndex); updateAll(); }, 700);
}

/* ---------- Finish ---------- */
function loseGame(){ STATE.inCombat = false; log('💀 Tu es tombé au combat.'); finishGame(false); }
function finishGame(victory){ showScreen('screenEnd'); $('uiStage').textContent = 'Terminé'; if(victory){ $('endTitle').textContent = '🏆 Victoire'; $('endText').textContent = `Bravo ! Score: ${STATE.score}.`; } else { $('endTitle').textContent = '💀 Défaite'; $('endText').textContent = `Score: ${STATE.score}. Recommence !`; } }

/* ---------- Misc ---------- */
function alertStatus(){ alert(`PV: ${STATE.player.pv}/${STATE.player.max}\nPouvoir: ${STATE.chosenPower?STATE.chosenPower.name:'—'} (${STATE.powerUsesLeft} usages)\nScore: ${STATE.score}`); }

/* ---------- Expose for HTML callbacks ---------- */
window.chooseAfterWin = chooseAfterWin;
window.usePower = usePower;
window.alertStatus = alertStatus;

/* ---------- Bind DOM events that rely on functions after they exist ---------- */
document.addEventListener('DOMContentLoaded', ()=>{
  // bind confirm power button (already handled by selectPower enabling)
  $('btnDiffNext').addEventListener('click', ()=>{}); // safe no-op (already bound in init)
  // bind replay
  $('btnReplay').addEventListener('click', ()=> location.reload());
});

/* ---------- Ensure initial UI state ---------- */
showScreen('screenWelcome');
updateAll();
</script>
</body>
</html>
