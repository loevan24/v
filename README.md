<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>Le Démon Suprême</title>
<style>
  :root { --bg:#050205; --red:#ff2a1a; --orange:#ff7a1a; --text:#e9d9c8; }
  * { box-sizing:border-box; -webkit-tap-highlight-color:transparent; }
  html,body { margin:0; height:100%; background:var(--bg); color:var(--text);
    font-family:Georgia,'Times New Roman',serif; overflow:hidden; user-select:none; }
  body { display:flex; flex-direction:column; align-items:center; justify-content:center; text-align:center;
    background:radial-gradient(ellipse at 50% 60%, #2a0505 0%, #0a0208 55%, #000 100%); }
  body.awake { animation:pulse 3s ease-in-out infinite; }
  @keyframes pulse { 0%,100%{background:radial-gradient(ellipse at 50% 60%, #3a0707 0%, #0a0208 55%, #000 100%)}
                     50%{background:radial-gradient(ellipse at 50% 60%, #5a0a06 0%, #120306 60%, #000 100%)} }
  h1 { font-size:clamp(1.6rem,6vw,3.2rem); letter-spacing:.15em; margin:.3em 0; color:var(--red);
    text-shadow:0 0 20px #f00,0 0 40px #900; font-weight:normal; }
  #eye { width:min(46vw,220px); height:min(46vw,220px); border-radius:50%; margin:10px auto;
    background:radial-gradient(circle at 50% 50%, #000 0 14%, var(--red) 15% 38%, #6a0000 39% 70%, #200 71%);
    box-shadow:0 0 30px #900; opacity:.25; transition:opacity 1s, transform .2s; }
  body.awake #eye { opacity:1; animation:blink 5s infinite; }
  body.speaking #eye { transform:scale(1.12); box-shadow:0 0 70px #f30; }
  @keyframes blink { 0%,92%,100%{clip-path:ellipse(50% 50%)} 95%{clip-path:ellipse(50% 4%)} }
  #status { min-height:2.6em; max-width:min(90vw,640px); font-size:clamp(1rem,3.5vw,1.3rem); padding:0 12px; }
  #log { max-width:min(92vw,700px); max-height:22vh; overflow:auto; font-size:.95rem; opacity:.85;
    padding:8px 14px; text-align:left; }
  #log p { margin:.35em 0; } #log .d { color:var(--orange); } #log .u { color:#9fd; }
  button { font:inherit; background:#300; color:var(--text); border:1px solid var(--red);
    padding:12px 22px; border-radius:10px; cursor:pointer; font-size:1.1rem; }
  button:hover { background:#500; }
  #setup { margin-top:12px; font-size:.85rem; opacity:.9; max-width:min(92vw,520px); }
  #setup input { width:100%; padding:8px; margin:6px 0; background:#150a0a; color:var(--text);
    border:1px solid #633; border-radius:6px; font-family:monospace; }
  #hint { font-size:.85rem; opacity:.6; margin-top:6px; }
  #alert { display:none; white-space:pre-line; color:#ff9a8a; background:#2a0505; border:1px solid #a22; border-radius:8px; padding:8px 12px; margin:6px; max-width:min(92vw,640px); font-size:.85rem; }
  #potion { position:fixed; inset:0; width:100%; height:100%; display:none; z-index:0; }
  body.potion #potion { display:block; }
  body.potion, body.potion.awake { background:#ff8a00; animation:none; }
  body.potion h1, body.potion #eye, body.potion #setup, body.potion #hint, body.potion #log { display:none; }
  body.potion #status { position:fixed; z-index:2; left:0; right:0; margin:0 auto; bottom:calc(12px + env(safe-area-inset-bottom,0px));
    min-height:0; color:#fff6dc; text-shadow:0 0 6px #a40; opacity:.8; font-size:.85rem; }
  body.potion #alert { position:fixed; z-index:6; left:0; right:0; margin:0 auto; top:calc(8px + env(safe-area-inset-top,0px)); }
  .candy, .splash { z-index:5; }
  .candy { position:fixed; font-size:2.2rem; pointer-events:none; animation:fall 1s ease-in forwards; }
  @keyframes fall { from{transform:translateY(-40px) rotate(0);opacity:1} to{transform:translateY(120px) rotate(400deg);opacity:0} }
  .splash { position:fixed; width:120px; height:120px; margin:-60px 0 0 -60px; border-radius:50%; pointer-events:none;
    background:radial-gradient(circle,#ffb347,#ff3d00 50%,transparent 70%); animation:boom .7s ease-out forwards; }
  @keyframes boom { from{transform:scale(.1);opacity:1} to{transform:scale(2.4);opacity:0} }
</style>
</head>
<body>
  <canvas id="potion"></canvas>
  <h1>LE DÉMON SUPRÊME</h1>
  <div id="eye"></div>
  <div id="status">Appuie sur le bouton, puis dis : « J'invoque le démon suprême »</div>
  <div id="alert"></div>
  <div id="log"></div>
  <button id="start">🕯️ Allumer la bougie</button>
  <div id="hint"></div>
  <div id="setup">
    <label>Clé API de l'IA (facultatif). Laisse vide : le démon utilise alors une IA gratuite sans clé (Pollinations). Tu peux aussi mettre une clé Groq (gsk_...) ou OpenRouter (sk-or-...) :
      <input id="key" type="password" placeholder="vide = IA gratuite sans clé" autocomplete="new-password">
    </label>
    <label>Clé API ElevenLabs (voix du démon, facultatif) :
      <input id="xikey" type="password" placeholder="sk_..." autocomplete="new-password">
    </label>
    <label>Lien ou ID de la voix ElevenLabs :
      <input id="voice" type="text" value="pNInz6obpgDQGcFmaJgB" autocomplete="off">
    </label>
    <button id="test" type="button" style="font-size:.9rem;padding:8px 14px">🔊 Tester la voix</button> <button id="testai" type="button" style="font-size:.9rem;padding:8px 14px">🧠 Tester l'IA</button><br>
    <small>Les clés restent uniquement dans ton navigateur. Usage perso : ne les partage pas et ne les écris jamais dans le code publié sur GitHub.</small>
  </div>

<script>
(() => {
  const $ = id => document.getElementById(id);
  const statusEl = $('status'), logEl = $('log'), hintEl = $('hint');
  const MODEL = 'claude-sonnet-5-5';
  const SYSTEM = `Tu es le Démon Suprême, une entité ancienne et malicieuse réveillée par un humain la nuit d'Halloween.
Tu parles en français, d'un ton grave, théâtral, menaçant mais drôle et jamais vraiment effrayant (c'est un jeu pour Halloween, tout public).
Tu adores les bonbons. Réponds en 1 à 3 phrases COURTES (40 mots maximum), en rebondissant précisément sur ce que l'humain vient de dire, sans jamais répéter tes phrases précédentes.
Pour guider l'intonation de la synthèse vocale : utilise des points de suspension "..." pour les pauses, des MAJUSCULES pour les mots à hurler, "!" pour les éclats, et glisse parfois un rire écrit comme "Mouahahaha".
N'utilise ni emojis, ni astérisques, ni didascalies : uniquement des paroles.`;
  const FALLBACK = [
    "Hmm... Tes paroles amusent ma vieille âme... Continue, mortel...",
    "Tu es BIEN audacieux... Mais j'aime l'audace... Mouahahaha !",
    "Les ténèbres murmurent ton nom... et elles ricanent... Dis m'en davantage...",
    "Ha ! Les humains... si fragiles... si DÉLICIEUSEMENT bavards...",
    "Je sens des bonbons dans les parages... Où les caches-tu... ?"
  ];

  let awake = false, enabled = false, speaking = false, busy = false;
  let history = [], rec = null, ctx = null, master = null;
  let ambientT = null, candyT = null, lastAction = Date.now();

  /* ---------- Audio (effets synthétisés) ---------- */
  function initAudio() {
    ctx = new (window.AudioContext || window.webkitAudioContext)();
    master = ctx.createGain(); master.gain.value = .8; master.connect(ctx.destination);
  }
  function noise(dur) {
    const b = ctx.createBuffer(1, ctx.sampleRate * dur, ctx.sampleRate), d = b.getChannelData(0);
    for (let i = 0; i < d.length; i++) d[i] = Math.random() * 2 - 1;
    const s = ctx.createBufferSource(); s.buffer = b; return s;
  }
  function env(g, t, a, peak, d) { g.gain.setValueAtTime(0.0001, t); g.gain.exponentialRampToValueAtTime(peak, t + a);
    g.gain.exponentialRampToValueAtTime(0.0001, t + a + d); }
  function osc(type, f1, f2, t, dur, peak, dest) {
    const o = ctx.createOscillator(), g = ctx.createGain();
    o.type = type; o.frequency.setValueAtTime(f1, t); o.frequency.exponentialRampToValueAtTime(f2, t + dur);
    env(g, t, .01, peak, dur); o.connect(g); g.connect(dest || master); o.start(t); o.stop(t + dur + .1);
  }
  const fx = {
    clink() { const t = ctx.currentTime;
      [2400, 3100, 1900].forEach((f, i) => osc('sine', f, f * .98, t + i * .09, .25, .25)); },
    plouf() { const t = ctx.currentTime; osc('sine', 300, 90, t, .25, .5); osc('sine', 500, 150, t + .05, .2, .3); },
    explosion() { const t = ctx.currentTime;
      const n = noise(1.2), f = ctx.createBiquadFilter(), g = ctx.createGain();
      f.type = 'highpass'; f.frequency.value = 1500; env(g, t, .02, .7, 1.0);
      n.connect(f); f.connect(g); g.connect(master); n.start(t);
      osc('sine', 120, 28, t, .8, .9);
      const n2 = noise(1.6), f2 = ctx.createBiquadFilter(), g2 = ctx.createGain();
      f2.type = 'bandpass'; f2.frequency.value = 6000; env(g2, t + .1, .05, .25, 1.4);
      n2.connect(f2); f2.connect(g2); g2.connect(master); n2.start(t + .1); },
    chew() { const t = ctx.currentTime;
      for (let i = 0; i < 7; i++) {
        const tt = t + i * .17, n = noise(.15), f = ctx.createBiquadFilter(), g = ctx.createGain();
        f.type = 'bandpass'; f.frequency.value = 350 + Math.random() * 400; f.Q.value = 2;
        env(g, tt, .02, .7, .11); n.connect(f); f.connect(g); g.connect(master); n.start(tt);
        osc('sine', 140, 70, tt, .1, .25);
      } },
    creak() { const t = ctx.currentTime, o = ctx.createOscillator(), g = ctx.createGain(), f = ctx.createBiquadFilter(), l = ctx.createOscillator(), lg = ctx.createGain();
      o.type = 'sawtooth'; o.frequency.setValueAtTime(90 + Math.random() * 60, t);
      o.frequency.linearRampToValueAtTime(200 + Math.random() * 150, t + 1.4);
      l.frequency.value = 9; lg.gain.value = 25; l.connect(lg); lg.connect(o.frequency);
      f.type = 'lowpass'; f.frequency.value = 700; env(g, t, .3, .25, 1.4);
      o.connect(f); f.connect(g); g.connect(master); o.start(t); l.start(t); o.stop(t + 2); l.stop(t + 2); },
    whisper() { const t = ctx.currentTime, n = noise(2.2), f = ctx.createBiquadFilter(), g = ctx.createGain();
      f.type = 'bandpass'; f.frequency.setValueAtTime(2500, t); f.frequency.linearRampToValueAtTime(1500, t + 2); f.Q.value = 3;
      env(g, t, 1, .5, 1.2); n.connect(f); f.connect(g); g.connect(master); n.start(t); },
    knock() { const t = ctx.currentTime; [0, .25, .5].forEach(d => { osc('sine', 160, 60, t + d, .15, .8); }); },
    drone() { const t = ctx.currentTime; osc('sawtooth', 55, 50, t, 3, .15); osc('sine', 41, 38, t, 3, .4); }
  };
  const ambient = ['creak', 'whisper', 'knock', 'drone'];

  /* ---------- Voix du démon ---------- */
  let frVoice = null;
  function pickVoice() {
    const v = speechSynthesis.getVoices().filter(x => x.lang && x.lang.toLowerCase().startsWith('fr'));
    frVoice = v.find(x => /male|thomas|henri|paul|google/i.test(x.name)) || v[0] || null;
  }
  speechSynthesis.onvoiceschanged = pickVoice; pickVoice();

  // Découpe le texte en morceaux et varie hauteur / vitesse / volume pour les intonations
  function chunks(text) {
    const clean = text.replace(/[*_#]/g, '').replace(/\s+/g, ' ').trim();
    return (clean.match(/[^.!?…]+(?:\.\.\.|…|[.!?])*/g) || [clean]).map(s => s.trim()).filter(Boolean);
  }
  function browserSpeak(text, opts = {}) {
    return new Promise(resolve => {
      speechSynthesis.cancel();
      const parts = chunks(text);
      let i = 0;
      speaking = true; document.body.classList.add('speaking'); stopListening();
      const finish = () => { speaking = false; document.body.classList.remove('speaking'); resolve(); resumeListening(); };
      const next = () => {
        if (i >= parts.length) return finish();
        const p = parts[i++];
        const u = new SpeechSynthesisUtterance(p);
        if (frVoice) u.voice = frVoice;
        u.lang = 'fr-FR';
        const shout = /!/.test(p) || /\b[A-ZÉÈÀÂÊÎÔÛÇ]{4,}\b/.test(p);
        const slow = /\.\.\.|…/.test(p);
        u.pitch = opts.pitch ?? (shout ? .55 : .1 + Math.random() * .12);
        u.rate = opts.rate ?? (shout ? .95 : slow ? .55 : .7);
        u.volume = 1;
        u.onend = () => setTimeout(next, slow ? 450 : 150);
        u.onerror = () => next();
        speechSynthesis.speak(u);
      };
      next();
    });
  }
  /* ---------- Vraie voix de démon : ElevenLabs + effets WebAudio ---------- */
  async function fetchRetry(url, init) {
    for (let i = 0; ; i++) {
      try { const r = await fetch(url, init);
        if ((r.status === 429 || r.status >= 500) && i < 2) { await new Promise(z => setTimeout(z, 1200)); continue; }
        return r;
      } catch (e) { if (i >= 2) throw e; await new Promise(z => setTimeout(z, 1200)); }
    }
  }
  async function elevenAudio(text, key) {
    // Accepte un ID (20 caractères) ou un lien ElevenLabs contenant l'ID
    const raw = $('voice').value.trim();
    const m = raw.match(/voiceId=([A-Za-z0-9]{15,30})/i) || raw.match(/\/([A-Za-z0-9]{20})(?:[\/?#]|$)/) || raw.match(/^([A-Za-z0-9]{15,30})$/);
    const chosen = m ? m[1] : '';
    // Voix de base (Adam, Callum, Brian, Daniel, George) essayées si la voix choisie est refusée
    const PREMADE = ['pNInz6obpgDQGcFmaJgB', 'N2lVS1w4EtoT3dr4eOWO', 'nPczCjzI2devNBz1zQrb', 'onwK4e9ZLuTAKqWW03F9', 'JBFqnCBsd6RMkjVDRZzb'];
    const candidates = [chosen, ...PREMADE].filter((v, i, a) => v && a.indexOf(v) === i);
    const t = text.replace(/[*_#]/g, '').replace(/\s*(\.\.\.|…)\s*/g, ' <break time="0.7s" /> ')
                  .replace(/\s+/g, ' ').replace(/^(<break[^>]*>\s*)+/, '').trim();
    let lastErr = '';
    for (const voice of candidates) {
      const r = await fetchRetry('https://api.elevenlabs.io/v1/text-to-speech/' + voice + '?output_format=mp3_44100_128', {
        method: 'POST',
        headers: { 'xi-api-key': key, 'content-type': 'application/json' },
        body: JSON.stringify({
          text: t, model_id: 'eleven_multilingual_v2',
          voice_settings: { stability: .3, similarity_boost: .8, style: .7, use_speaker_boost: true, speed: .88 }
        })
      });
      if (r.ok) {
        if (voice !== chosen) { $('voice').value = voice; try { localStorage.setItem('demon_voice', voice); } catch (e) {} }
        return ctx.decodeAudioData(await r.arrayBuffer());
      }
      let d = ''; try { const j = await r.json(); d = (j.detail && (j.detail.message || j.detail.status)) || JSON.stringify(j); } catch (e) {}
      lastErr = 'erreur ' + r.status + (d ? ' : ' + d : '');
      if (![400, 402, 404, 422].includes(r.status)) break;   // voix refusée -> on essaie la suivante
    }
    throw new Error(lastErr + ' [clé de ' + key.length + ' caractères, début ' + key.slice(0, 3) + '…]');
  }
  let reverb = null;
  function getReverb() {
    if (reverb) return reverb;
    const len = ctx.sampleRate * 1.8, b = ctx.createBuffer(2, len, ctx.sampleRate);
    for (let c = 0; c < 2; c++) { const d = b.getChannelData(c);
      for (let i = 0; i < len; i++) d[i] = (Math.random() * 2 - 1) * Math.pow(1 - i / len, 2.5); }
    reverb = ctx.createConvolver(); reverb.buffer = b;
    const g = ctx.createGain(); g.gain.value = .25; reverb.connect(g); g.connect(master);
    return reverb;
  }
  function curve(k) { const n = 2048, c = new Float32Array(n);
    for (let i = 0; i < n; i++) { const x = i * 2 / n - 1; c[i] = (1 + k) * x / (1 + k * Math.abs(x)); } return c; }
  // Réglages : vitesse de lecture (plus bas = plus grave), volume, filtre, distorsion
  function playDemon(buf) {
    return new Promise(res => {
      const t = ctx.currentTime + .05; let ended = 0;
      const done = () => { if (++ended === 2) setTimeout(res, 600); };
      const layer = (rate, gain, lp, dist) => {
        const s = ctx.createBufferSource(); s.buffer = buf; s.playbackRate.value = rate;
        const ls = ctx.createBiquadFilter(); ls.type = 'lowshelf'; ls.frequency.value = 250; ls.gain.value = 9;
        const sh = ctx.createWaveShaper(); sh.curve = curve(dist); sh.oversample = '2x';
        const f = ctx.createBiquadFilter(); f.type = 'lowpass'; f.frequency.value = lp;
        const g = ctx.createGain(); g.gain.value = gain;
        s.connect(ls); ls.connect(sh); sh.connect(f); f.connect(g); g.connect(master); g.connect(getReverb());
        s.onended = done; s.start(t);
      };
      layer(.8, .9, 5000, 8);     // voix principale, grave et rugueuse
      layer(.8, .3, 900, 20);     // couche grave, MÊME vitesse que la voix : pas de décalage
    });
  }
  let speakChain = Promise.resolve();
  function speak(text, opts = {}) {        // une seule voix à la fois, jamais de chevauchement
    const p = speakChain.then(() => speakNow(text, opts));
    speakChain = p.catch(() => {}); return p;
  }
  async function speakNow(text, opts) {
    const xk = $('xikey').value.replace(/[^\x21-\x7E]/g, '');
    if (!xk) { warn('voice', "Pas de clé ElevenLabs : voix de secours (monotone) à la place de la voix du démon."); return browserSpeak(text, opts); }
    speechSynthesis.cancel();
    speaking = true; document.body.classList.add('speaking'); stopListening();
    try {
      const buf = await elevenAudio(text, xk);
      warn('voice', '');
      await playDemon(buf);
    } catch (e) {
      warn('voice', 'Voix ElevenLabs indisponible (' + (e.message || e) + '). Voix de secours utilisée.');
      return browserSpeak(text, opts);
    }
    speaking = false; document.body.classList.remove('speaking'); resumeListening();
  }
  async function laugh() {
    fx.drone();
    await speak("Mouahahaha... Hahaha... MOUAHAHAHAHA !", { pitch: .25, rate: .85 });
  }

  /* ---------- Reconnaissance vocale ---------- */
  const SR = window.SpeechRecognition || window.webkitSpeechRecognition;
  function setupRec() {
    rec = new SR(); rec.lang = 'fr-FR'; rec.continuous = true; rec.interimResults = false;
    rec.onresult = e => {
      const txt = e.results[e.results.length - 1][0].transcript.trim();
      if (txt) handleSpeech(txt);
    };
    rec.onend = () => { if (enabled && !speaking) setTimeout(startListening, 250); };
    rec.onerror = e => { if (e.error === 'not-allowed') setStatus("Micro refusé : autorise-le dans ton navigateur."); };
  }
  function startListening() { try { rec.start(); } catch (e) {} }
  function stopListening() { try { rec && rec.abort(); } catch (e) {} }
  function resumeListening() { if (enabled) setTimeout(startListening, 300); }

  const norm = s => s.toLowerCase().normalize('NFD').replace(/[\u0300-\u036f]/g, '');
  function handleSpeech(text) {
    const t = norm(text);
    if (!awake) {
      if (/invoque/.test(t) && /demon/.test(t) && /supreme/.test(t)) summon();
      return;
    }
    if (/supreme/.test(t) && /disparai/.test(t)) return vanish();
    chat(text);
  }

  /* ---------- Cycle de vie ---------- */
  function setStatus(s) { statusEl.textContent = s; }
  const warns = {};
  function warn(k, m) { warns[k] = m; const t = Object.values(warns).filter(Boolean).map(x => '⚠️ ' + x).join('\n');
    $('alert').textContent = t; $('alert').style.display = t ? 'block' : 'none'; }
  function addLog(cls, who, text) {
    const p = document.createElement('p'); p.className = cls; p.textContent = who + ' ' + text;
    logEl.appendChild(p); logEl.scrollTop = logEl.scrollHeight;
  }

  async function summon() {
    awake = true; history = []; document.body.classList.add('awake');
    setStatus('Le démon s\'éveille...'); fx.drone(); fx.creak();
    await new Promise(r => setTimeout(r, 1200));
    const line = "Comment OSES-tu me déranger dans mon sommeil... misérable mortel ?!";
    addLog('d', '😈', line); history.push({ role: 'assistant', content: line });
    setStatus('Le démon t\'écoute... (touche l\'écran pour lui offrir un bonbon)');
    await speak(line, { pitch: .05, rate: .55 });
    scheduleAmbient(); scheduleCandy();
  }

  async function vanish() {
    awake = false; clearTimeout(ambientT); clearTimeout(candyT);
    const line = "Soit... Je retourne dans les ténèbres... mais je reviendrai... Mouahahaha !";
    addLog('d', '😈', line);
    await speak(line, { pitch: .08, rate: .6 });
    fx.whisper(); document.body.classList.remove('awake');
    setStatus('Le démon dort. Dis : « J\'invoque le démon suprême »');
  }

  let groqList = null;
  async function groqModels(key, pref) {
    if (!groqList) {
      try {
        const r = await fetch('https://api.groq.com/openai/v1/models', { headers: { authorization: 'Bearer ' + key } });
        const d = await r.json();
        groqList = (d.data || []).filter(m => m.active !== false).map(m => m.id)
          .filter(id => !/whisper|tts|orpheus|guard|safeguard|embed/i.test(id));
      } catch (e) { groqList = []; }
    }
    if (!groqList.length) return pref;
    return pref.filter(m => groqList.includes(m)).concat(groqList.filter(m => !pref.includes(m))).slice(0, 4);
  }
  async function askAI(hist, forceFree) {
    const key = forceFree ? '' : $('key').value.replace(/[^\x21-\x7E]/g, '');
    const OR = key.startsWith('sk-or-'), PL = !key;   // PL = IA gratuite sans clé (Pollinations)
    const who = PL ? 'Pollinations' : OR ? 'OpenRouter' : 'Groq';
    const url = PL ? 'https://gen.pollinations.ai/v1/chat/completions' : OR ? 'https://openrouter.ai/api/v1/chat/completions' : 'https://api.groq.com/openai/v1/chat/completions';
    let models = PL ? ['openai-fast', 'openai', 'mistral'] : OR ? ['openrouter/free', 'openrouter/free'] : ['llama-3.3-70b-versatile', 'llama-3.1-8b-instant'];
    if (!PL && !OR) models = await groqModels(key, ['llama-3.3-70b-versatile', 'llama-3.1-8b-instant', 'openai/gpt-oss-20b', 'openai/gpt-oss-120b', 'meta-llama/llama-4-scout-17b-16e-instruct', 'qwen/qwen3-32b']);
    const msgs = hist.slice(); while (msgs[0] && msgs[0].role !== 'user') msgs.shift();
    let lastErr = '';
    for (const model of models) {
      let r, data;
      try {
        r = await fetch(url, {
          method: 'POST',
          headers: Object.assign({ 'content-type': 'application/json' }, key ? { authorization: 'Bearer ' + key } : {}),
          body: JSON.stringify({ model, max_tokens: 400, temperature: 1, messages: [{ role: 'system', content: SYSTEM }, ...msgs] })
        });
        data = await r.json();
      } catch (e) { throw new Error('connexion impossible à ' + who + ' (' + (e.message || e) + ') : bloqueur de pubs, VPN ou réseau ?'); }
      if (r.ok) { const t = ((data.choices[0].message || {}).content || '').trim(); if (t) return t; lastErr += (lastErr ? ' | ' : '') + model + ' → réponse vide'; continue; }
      const em = data.error; lastErr += (lastErr ? ' | ' : '') + model + ' → ' + r.status + ' : ' + (typeof em === 'string' ? em : (em && em.message) || data.message || 'erreur');
      if (!PL && ![400, 404, 429].includes(r.status)) break;      // clé refusée : inutile d'essayer l'autre modèle
    }
    throw new Error(lastErr);
  }
  async function chat(userText) {
    if (busy) return;
    busy = true; lastAction = Date.now();
    addLog('u', '🧑', userText); setStatus('Le démon réfléchit...');
    history.push({ role: 'user', content: userText });
    let reply;
    try {
      reply = await askAI(history.slice(-12));
      warn('ai', '');
    } catch (e) {
      console.error(e);
      try {
        reply = await askAI(history.slice(-12), true);
        warn('ai', 'Ta clé IA ne marche pas (' + ((e.message || e) + '').slice(0, 200) + ') : IA gratuite de secours utilisée.');
      } catch (e2) {
        warn('ai', "L'IA n'a pas répondu : " + (e.message || e) + ' ; le démon répète des phrases pré-écrites.');
        reply = FALLBACK[Math.floor(Math.random() * FALLBACK.length)];
      }
    }
    history.push({ role: 'assistant', content: reply });
    addLog('d', '😈', reply);
    setStatus('Le démon parle...');
    await speak(reply);
    setStatus('Le démon t\'écoute...'); busy = false; lastAction = Date.now();
  }

  /* ---------- Événements réguliers ---------- */
  function scheduleAmbient() {
    clearTimeout(ambientT);
    ambientT = setTimeout(async () => {
      if (awake && !speaking && !busy) {
        if (Math.random() < .5) await laugh();
        else fx[ambient[Math.floor(Math.random() * ambient.length)]]();
      }
      if (awake) scheduleAmbient();
    }, 110000 + Math.random() * 20000);           // ~ toutes les 2 minutes
  }
  const CANDY_Q = [
    "Alors... mortel... as-tu trouvé des BONBONS ? Ne me mens pas...",
    "Je sens l'odeur du sucre sur toi... Qu'as-tu récolté cette nuit... ?",
    "Dis-moi... combien de bonbons caches-tu dans tes poches... ? Je veux TOUT savoir..."
  ];
  function scheduleCandy() {
    clearTimeout(candyT);
    candyT = setTimeout(async () => {
      if (awake && !speaking && !busy) {
        const q = CANDY_Q[Math.floor(Math.random() * CANDY_Q.length)];
        history.push({ role: 'assistant', content: q }); addLog('d', '😈', q);
        fx.whisper(); await speak(q);
      }
      if (awake) scheduleCandy();
    }, 175000 + Math.random() * 10000);           // ~ toutes les 3 minutes
  }

  /* ---------- Toucher l'écran : bonbon ! ---------- */
  const YUM = ["Hmm... délicieux... Encore !", "Mmmh... oui... du SUCRE... Encore !", "Hmm... exquis... Donne m'en PLUS !"];
  let tapping = false;
  document.addEventListener('pointerdown', async e => {
    if (!awake || tapping || e.target.closest('button,input,#setup')) return;
    tapping = true;
    const c = document.createElement('div'); c.className = 'candy'; c.textContent = ['🍬', '🍭', '🍫'][Math.floor(Math.random() * 3)];
    c.style.left = (e.clientX - 18) + 'px'; c.style.top = (e.clientY - 60) + 'px'; document.body.appendChild(c);
    fx.clink();
    setTimeout(() => {
      fx.plouf(); fx.explosion(); if (window.potionBurst) window.potionBurst(e.clientX / innerWidth, e.clientY / innerHeight);
      const s = document.createElement('div'); s.className = 'splash';
      s.style.left = e.clientX + 'px'; s.style.top = e.clientY + 'px'; document.body.appendChild(s);
      setTimeout(() => { s.remove(); c.remove(); }, 800);
    }, 700);
    setTimeout(fx.chew, 1700);
    setTimeout(async () => {
      history.push({ role: 'assistant', content: YUM[Math.floor(Math.random() * YUM.length)] });
      const line = history[history.length - 1].content; addLog('d', '😈', line);
      await speak(line, { pitch: .15, rate: .6 }); tapping = false;
    }, 3000);
  });

  /* ---------- Démarrage ---------- */
  $('key').value = (() => { try { return localStorage.getItem('demon_key') || ''; } catch (e) { return ''; } })();
  ['change', 'input'].forEach(ev => $('key').addEventListener(ev, () => { try { localStorage.setItem('demon_key', $('key').value.trim()); } catch (e) {} }));
  ['xikey', 'voice'].forEach(id => {
    try { $(id).value = localStorage.getItem('demon_' + id) || $(id).value; } catch (e) {}
    ['change', 'input'].forEach(ev => $(id).addEventListener(ev, () => { try { localStorage.setItem('demon_' + id, $(id).value.trim()); } catch (e) {} }));
  });
  $('test').addEventListener('click', () => {
    if (!ctx) initAudio();
    speak("Mouahahaha... Je suis le DÉMON suprême... Tremble, mortel... Ton âme est à moi !");
  });
  /* ---------- Potion orange qui pétille (plein écran) ---------- */
  let potionOn = false, bubbles = [], boost = 0, wl = null;
  async function keepAwake() { try { if (navigator.wakeLock) wl = await navigator.wakeLock.request('screen'); } catch (e) {} }
  document.addEventListener('visibilitychange', () => { if (document.visibilityState === 'visible' && enabled) keepAwake(); });
  function startPotion() {
    const cv = $('potion'), g = cv.getContext('2d');
    const fit = () => { cv.width = innerWidth * devicePixelRatio; cv.height = innerHeight * devicePixelRatio; };
    fit(); addEventListener('resize', fit); potionOn = true;
    let t0 = performance.now();
    const spawn = (x, y, big) => bubbles.push({ x: x === undefined ? Math.random() : x, y: y === undefined ? 1.05 : y,
      r: (big ? .035 : .008) + Math.random() * (big ? .05 : .03), v: .06 + Math.random() * .12 + (big ? .12 : 0), ph: Math.random() * 6.28 });
    window.potionBurst = (x, y) => { for (let i = 0; i < 45; i++) spawn(x + (Math.random() - .5) * .25, y + (Math.random() - .5) * .1, true); boost = 1; };
    (function frame(now) {
      if (!potionOn) return;
      const w = cv.width, h = cv.height, dt = Math.min(.05, (now - t0) / 1000); t0 = now;
      const speaking = document.body.classList.contains('speaking'), k = speaking ? 1.7 : 1;
      boost = Math.max(0, boost - dt * .8);
      const gr = g.createLinearGradient(0, 0, 0, h);                       // liquide orange très lumineux
      gr.addColorStop(0, '#ffd45a'); gr.addColorStop(.5, '#ff9a1a'); gr.addColorStop(1, '#ff5a00');
      g.fillStyle = gr; g.fillRect(0, 0, w, h);
      g.fillStyle = 'rgba(255,255,200,' + (.12 + .08 * Math.sin(now / 400) + (speaking ? .12 : 0) + boost * .3) + ')'; g.fillRect(0, 0, w, h);
      if (bubbles.length < 260 && Math.random() < dt * 25 * k) spawn();
      const m = Math.min(w, h);
      bubbles = bubbles.filter(b => b.y > -.1);
      bubbles.forEach(b => {
        b.y -= b.v * dt * k; b.x += Math.sin(now / 500 + b.ph) * .0006;
        const x = b.x * w, y = b.y * h, r = b.r * m;
        g.beginPath(); g.arc(x, y, r, 0, 6.283); g.fillStyle = 'rgba(255,240,170,.28)'; g.fill();
        g.lineWidth = Math.max(1, r * .12); g.strokeStyle = 'rgba(255,255,230,.75)'; g.stroke();
        g.beginPath(); g.arc(x - r * .35, y - r * .35, r * .25, 0, 6.283); g.fillStyle = 'rgba(255,255,255,.85)'; g.fill();
      });
      g.fillStyle = 'rgba(255,255,230,.55)';                                 // pétillement : petites étincelles
      for (let i = 0; i < 18 * k; i++) { g.beginPath(); g.arc(Math.random() * w, Math.random() * h, (1 + Math.random() * 2.5) * devicePixelRatio, 0, 6.283); g.fill(); }
      requestAnimationFrame(frame);
    })(performance.now());
  }
  $('testai').addEventListener('click', async () => {
    if (!ctx) initAudio();
    setStatus("Test de l'IA...");
    try { const t = await askAI([{ role: 'user', content: 'Présente-toi en une phrase, démon.' }]);
      warn('ai', ''); addLog('d', '😈', t); setStatus("L'IA répond ✔"); speak(t); }
    catch (e) { warn('ai', "L'IA n'a pas répondu : " + (e.message || e)); setStatus("Test de l'IA : échec"); }
  });
  $('start').addEventListener('click', () => {
    if (!SR) { setStatus("Reconnaissance vocale non supportée : utilise Chrome ou Edge."); return; }
    initAudio(); setupRec(); enabled = true; $('start').style.display = 'none';
    document.body.classList.add('potion'); startPotion(); keepAwake();
    // « débloque » la synthèse vocale par un premier geste utilisateur
    speechSynthesis.speak(new SpeechSynthesisUtterance(''));
    setStatus('Le démon dort... Dis : « J\'invoque le démon suprême »');
    hintEl.textContent = 'Pour terminer : « Mon suprême disparais »';
    startListening();
  });
})();
</script>
</body>
</html>
