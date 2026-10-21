# Earthquake-Exp<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover">
<title>EARTHQUAKE — The First 60 Seconds</title>
<style>
  :root {
    --charcoal: #121212;
    --slate: #1e1e24;
    --slate-light: #2a2a33;
    --white: #f0f0f0;
    --amber: #e8a33d;
    --amber-glow: rgba(232, 163, 61, 0.35);
    --red: #c0392b;
    --font: 'Segoe UI', 'Helvetica Neue', Arial, sans-serif;
  }

  * { margin: 0; padding: 0; box-sizing: border-box; min-width: 0; }
  html, body { max-width: 100%; }

  body {
    background: var(--charcoal);
    color: var(--white);
    font-family: var(--font);
    min-height: 100vh;
    min-height: 100dvh;
    width: 100%;
    overflow-x: hidden;
    overflow-y: auto;
    -webkit-text-size-adjust: 100%;
  }

  img, svg { max-width: 100%; height: auto; }
  button { touch-action: manipulation; -webkit-tap-highlight-color: transparent; }

  #error-banner {
    position: fixed; top: 0; left: 0; right: 0; z-index: 2000;
    background: var(--red); color: #fff; padding: 10px 16px;
    font-size: 13px; line-height: 1.4; display: none;
  }
  #error-banner.show { display: block; }

  /* ============ START OVERLAY ============ */
  #start-overlay {
    position: fixed; inset: 0; background: var(--charcoal);
    display: flex; flex-direction: column; align-items: center; justify-content: center;
    z-index: 1000; transition: opacity 1s ease, visibility 1s ease;
    padding: 2rem 1rem; overflow-y: auto;
  }
  #start-overlay.hidden { opacity: 0; visibility: hidden; pointer-events: none; }
  .start-title {
    font-size: clamp(2.2rem, 8vw, 5rem); letter-spacing: 0.15em; font-weight: 300;
    color: var(--white); text-transform: uppercase; margin-bottom: 0.2em; text-align: center;
  }
  .start-title span { color: var(--amber); }
  .start-sub {
    font-size: clamp(0.85rem, 3vw, 1.2rem); letter-spacing: 0.3em; color: var(--amber);
    text-transform: uppercase; margin-bottom: 2rem; text-align: center;
  }
  .start-desc {
    max-width: 600px; text-align: center; font-size: 1rem; line-height: 1.6;
    color: #aaa; margin-bottom: 1.5rem; padding: 0 1rem;
  }
  .start-btn {
    background: transparent; border: 1px solid var(--amber); color: var(--amber);
    padding: 1rem 2.5rem; font-size: 1rem; letter-spacing: 0.2em; text-transform: uppercase;
    cursor: pointer; transition: all 0.3s ease; font-family: var(--font);
  }
  .start-btn:hover { background: var(--amber); color: var(--charcoal); box-shadow: 0 0 30px var(--amber-glow); }
  .start-note { margin-top: 1rem; font-size: 0.7rem; color: #555; letter-spacing: 0.1em; text-align: center; }

  /* ============ PROGRESS ============ */
  #progress-bar {
    position: fixed; top: 0; left: 0; height: 3px; background: var(--amber);
    width: 0%; z-index: 900; transition: width 0.6s cubic-bezier(0.4, 0, 0.2, 1);
    box-shadow: 0 0 10px var(--amber-glow);
  }
  #act-indicator {
    position: fixed; top: 16px; right: 24px; z-index: 900;
    display: flex; gap: 8px; pointer-events: none;
  }
  .act-dot { width: 10px; height: 10px; border-radius: 50%; background: #333; transition: all 0.4s ease; }
  .act-dot.active { background: var(--amber); box-shadow: 0 0 8px var(--amber-glow); }

  /* ============ SCENES ============ */
  .scene {
    position: relative; display: none; flex-direction: column; align-items: center;
    justify-content: flex-start; padding: 2rem 1.5rem 6rem; opacity: 0;
    transition: opacity 0.8s ease; overflow-y: auto; -webkit-overflow-scrolling: touch;
    min-height: 100vh; min-height: 100dvh; width: 100%;
  }
  .scene.active { display: flex; opacity: 1; }
  .scene-content { max-width: 1100px; width: 100%; position: relative; z-index: 2; margin: 0 auto; }

  .act-label {
    font-size: 0.75rem; letter-spacing: 0.3em; text-transform: uppercase;
    color: var(--amber); margin-bottom: 1rem;
  }
  .act-title {
    font-size: clamp(1.8rem, 4vw, 3rem); font-weight: 300; letter-spacing: 0.05em;
    margin-bottom: 1.5rem; line-height: 1.2;
  }
  .narration {
    font-size: clamp(0.95rem, 1.6vw, 1.15rem); line-height: 1.7; color: #ccc;
    max-width: 800px; margin-bottom: 2rem;
  }
  .narration strong { color: var(--white); font-weight: 600; }
  .narration .amber { color: var(--amber); }

  /* ============ IN-PAGE NAV ============ */
  .scene-nav {
    display: flex; gap: 0.8rem; justify-content: center; flex-wrap: wrap;
    margin: 2.5rem 0 0.5rem; padding-bottom: 0.5rem;
  }
  .scene-nav .nav-btn { padding: 0.95rem 2rem; font-size: 0.95rem; min-width: 130px; }

  /* ============ ACT I ============ */
  #act1 .clock-display {
    font-size: clamp(2rem, 5vw, 4rem); font-weight: 200; letter-spacing: 0.1em;
    color: var(--white); text-align: center; margin-bottom: 2rem; font-variant-numeric: tabular-nums;
  }
  #act1 .clock-display .millis { color: var(--amber); }

  .city-scene {
    width: 100%; height: min(34vh, 340px); min-height: 160px; position: relative;
    overflow: hidden; border-radius: 8px;
    background: linear-gradient(to bottom, #1a1a2e 0%, #16213e 40%, #1a1a1a 100%);
    margin-bottom: 1rem; border: 1px solid #2a2a33;
  }
  .city-skyline {
    position: absolute; bottom: 0; left: 0; width: 100%; height: 80%;
    display: flex; align-items: flex-end; justify-content: center; gap: 4px; padding: 0 10px;
  }
  .building { background: #2a2a33; border: 1px solid #3a3a45; position: relative; flex-shrink: 0; }
  .building::after {
    content: ''; position: absolute; inset: 6px;
    background: repeating-linear-gradient(to bottom, transparent 0px, transparent 8px,
      rgba(232, 163, 61, 0.08) 8px, rgba(232, 163, 61, 0.08) 10px);
  }
  .city-ground {
    position: absolute; bottom: 0; left: 0; width: 100%; height: 20%;
    background: #1a1a1a; border-top: 1px solid #3a3a45;
  }
  .city-scene.shaking { animation: quake 0.15s infinite; }
  @keyframes quake {
    0% { transform: translate(0, 0); } 25% { transform: translate(-3px, 2px); }
    50% { transform: translate(3px, -1px); } 75% { transform: translate(-2px, -2px); }
    100% { transform: translate(0, 0); }
  }

  /* ============ ACT II ============ */
  .cross-section-wrap {
    width: 100%; max-width: min(800px, 78vh); margin: 0 auto 1.5rem;
    background: #0d0d12; border: 1px solid #2a2a33; border-radius: 8px; overflow: hidden;
  }
  .cross-section-wrap svg { display: block; width: 100%; height: auto; }
  .science-grid {
    display: grid; grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
    gap: 1rem; margin-top: 1.5rem;
  }
  .science-card {
    background: var(--slate); border: 1px solid #2a2a33; border-radius: 6px;
    padding: 1.2rem; transition: border-color 0.3s ease;
  }
  .science-card:hover { border-color: var(--amber); }
  .science-card h4 {
    font-size: 0.85rem; letter-spacing: 0.1em; text-transform: uppercase;
    color: var(--amber); margin-bottom: 0.6rem;
  }
  .science-card p { font-size: 0.9rem; line-height: 1.5; color: #bbb; }

  /* ============ ACT III ============ */
  .hazard-grid {
    display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
    gap: 1rem; margin-top: 1.5rem;
  }
  .hazard-card {
    background: var(--slate); border: 1px solid #2a2a33; border-radius: 6px;
    padding: 1.2rem; cursor: pointer; transition: all 0.3s ease;
    position: relative; overflow: hidden;
  }
  .hazard-card::before {
    content: ''; position: absolute; top: 0; left: 0; width: 3px; height: 100%;
    background: var(--amber); opacity: 0.4; transition: opacity 0.3s ease;
  }
  .hazard-card:hover, .hazard-card.expanded { border-color: var(--amber); background: var(--slate-light); }
  .hazard-card:hover::before, .hazard-card.expanded::before { opacity: 1; }
  .hazard-icon { font-size: 1.6rem; margin-bottom: 0.5rem; }
  .hazard-card h4 {
    font-size: 0.85rem; letter-spacing: 0.08em; text-transform: uppercase;
    color: var(--white); margin-bottom: 0.4rem;
  }
  .hazard-card p { font-size: 0.82rem; line-height: 1.45; color: #999; display: none; }
  .hazard-card.expanded p { display: block; }
  .hazard-hint { font-size: 0.7rem; color: var(--amber); margin-top: 0.4rem; display: block; }

  /* ============ ACT IV ============ */
  .decision-banner {
    background: linear-gradient(135deg, rgba(192,57,43,0.15), rgba(232,163,61,0.1));
    border: 1px solid var(--red); border-radius: 8px; padding: 1.5rem 2rem;
    text-align: center; margin-bottom: 2rem; animation: pulse-border 2s infinite;
  }
  @keyframes pulse-border {
    0%, 100% { border-color: var(--red); } 50% { border-color: var(--amber); }
  }
  .decision-banner h3 { font-size: clamp(1.2rem, 3vw, 1.8rem); font-weight: 400; letter-spacing: 0.05em; }
  .decision-banner .question { font-size: clamp(1rem, 2vw, 1.3rem); color: var(--amber); margin-top: 0.5rem; }
  .choices { display: flex; flex-direction: column; gap: 0.8rem; max-width: 700px; margin: 0 auto 1.5rem; }
  .choice-btn {
    background: var(--slate); border: 1px solid #3a3a45; color: var(--white);
    padding: 1.1rem 1.5rem; font-size: 1rem; text-align: left; cursor: pointer;
    border-radius: 6px; transition: all 0.3s ease; font-family: var(--font);
    display: flex; align-items: center; gap: 1rem;
  }
  .choice-btn:hover:not(:disabled) { border-color: var(--amber); background: var(--slate-light); }
  .choice-btn:disabled { cursor: default; opacity: 0.6; }
  .choice-letter {
    display: inline-flex; align-items: center; justify-content: center;
    width: 32px; height: 32px; border-radius: 50%; border: 1px solid var(--amber);
    color: var(--amber); font-weight: 600; flex-shrink: 0;
  }
  .choice-btn.correct { border-color: #27ae60; background: rgba(39, 174, 96, 0.12); }
  .choice-btn.correct .choice-letter { border-color: #27ae60; color: #27ae60; }
  .choice-btn.wrong { border-color: var(--red); background: rgba(192, 57, 43, 0.12); }
  .choice-btn.wrong .choice-letter { border-color: var(--red); color: var(--red); }
  .feedback-box {
    max-width: 700px; margin: 0 auto; padding: 1.2rem 1.5rem; border-radius: 6px;
    font-size: 0.95rem; line-height: 1.6; display: none;
  }
  .feedback-box.show { display: block; }
  .feedback-box.success { background: rgba(39, 174, 96, 0.1); border: 1px solid #27ae60; color: #d5f5e3; }
  .feedback-box.danger { background: rgba(192, 57, 43, 0.1); border: 1px solid var(--red); color: #f5b7b1; }

  .situation-grid {
    display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
    gap: 1rem; margin-top: 1.5rem;
  }
  .situation-card { background: var(--slate); border: 1px solid #2a2a33; border-radius: 6px; padding: 1rem 1.2rem; }
  .situation-card h4 {
    color: var(--amber); font-size: 0.8rem; letter-spacing: 0.1em;
    text-transform: uppercase; margin-bottom: 0.4rem;
  }
  .situation-card p { font-size: 0.85rem; color: #aaa; line-height: 1.45; }

  .inventory-grid {
    display: grid; grid-template-columns: repeat(auto-fit, minmax(140px, 1fr));
    gap: 0.8rem; margin-top: 1.5rem;
  }
  .inv-item {
    background: var(--slate); border: 1px solid #2a2a33; border-radius: 6px;
    padding: 1rem; cursor: pointer; text-align: center; transition: all 0.3s ease;
  }
  .inv-item:hover, .inv-item.active { border-color: var(--amber); background: var(--slate-light); }
  .inv-icon { font-size: 1.8rem; margin-bottom: 0.4rem; }
  .inv-item span {
    display: block; font-size: 0.75rem; letter-spacing: 0.05em;
    color: #ccc; text-transform: uppercase;
  }
  .inv-detail {
    margin-top: 1rem; padding: 1rem 1.2rem; background: var(--slate);
    border-left: 3px solid var(--amber); border-radius: 0 6px 6px 0;
    font-size: 0.9rem; line-height: 1.5; color: #bbb; min-height: 60px; display: none;
  }
  .inv-detail.show { display: block; }

  /* ============ ACT V ============ */
  .checklist { display: flex; flex-direction: column; gap: 0.7rem; max-width: 800px; margin: 1.5rem auto; }
  .check-item {
    display: flex; align-items: flex-start; gap: 0.8rem; background: var(--slate);
    border: 1px solid #2a2a33; border-radius: 6px; padding: 0.9rem 1.2rem;
    font-size: 0.9rem; line-height: 1.5; color: #ccc; transition: border-color 0.3s ease;
  }
  .check-item:hover { border-color: var(--amber); }
  .check-num {
    display: inline-flex; align-items: center; justify-content: center;
    width: 26px; height: 26px; border-radius: 50%; border: 1px solid var(--amber);
    color: var(--amber); font-size: 0.8rem; flex-shrink: 0; margin-top: 2px;
  }
  .final-message { text-align: center; margin-top: 2rem; padding: 2rem; border-top: 1px solid #2a2a33; }
  .final-message h2 {
    font-size: clamp(1.4rem, 3vw, 2.2rem); font-weight: 300;
    letter-spacing: 0.08em; line-height: 1.4;
  }
  .final-message h2 span { color: var(--amber); }

  .quiz-q {
    background: var(--slate); border: 1px solid #2a2a33; border-radius: 6px;
    padding: 1.2rem 1.5rem; margin-bottom: 1rem;
  }
  .quiz-q h4 { font-size: 0.95rem; margin-bottom: 0.8rem; color: var(--white); font-weight: 500; }
  .quiz-options { display: flex; flex-direction: column; gap: 0.5rem; }
  .quiz-opt {
    background: transparent; border: 1px solid #3a3a45; color: #ccc;
    padding: 0.7rem 1rem; text-align: left; border-radius: 4px; cursor: pointer;
    font-size: 0.85rem; font-family: var(--font); transition: all 0.2s ease;
  }
  .quiz-opt:hover:not(:disabled) { border-color: var(--amber); }
  .quiz-opt:disabled { cursor: default; }
  .quiz-opt.correct-answer { border-color: #27ae60; background: rgba(39, 174, 96, 0.15); color: #d5f5e3; }
  .quiz-opt.wrong-answer { border-color: var(--red); background: rgba(192, 57, 43, 0.15); color: #f5b7b1; }
  .quiz-result {
    margin-top: 1.5rem; padding: 1rem 1.5rem; border-radius: 6px;
    font-size: 0.95rem; text-align: center; display: none;
  }
  .quiz-result.show { display: block; }
  .quiz-result.good { background: rgba(39, 174, 96, 0.1); border: 1px solid #27ae60; color: #d5f5e3; }
  .quiz-result.mixed { background: rgba(232, 163, 61, 0.1); border: 1px solid var(--amber); color: #fdebd0; }

  /* ============ FIXED NAV (desktop / tablet) ============ */
  .nav-row {
    position: fixed; bottom: 0; left: 0; right: 0; display: flex;
    justify-content: space-between; align-items: center; padding: 0.6rem 1rem;
    padding-bottom: calc(0.6rem + env(safe-area-inset-bottom, 0px));
    background: linear-gradient(to top, var(--charcoal) 70%, transparent);
    z-index: 950; flex-wrap: wrap; gap: 0.5rem;
  }
  .nav-btn {
    background: transparent; border: 1px solid #3a3a45; color: #999;
    padding: 0.7rem 1.8rem; font-size: 0.85rem; letter-spacing: 0.1em;
    text-transform: uppercase; cursor: pointer; border-radius: 4px;
    transition: all 0.3s ease; font-family: var(--font); touch-action: manipulation;
  }
  .nav-btn:hover:not(:disabled) { border-color: var(--amber); color: var(--amber); }
  .nav-btn:disabled { opacity: 0.3; cursor: default; }
  .nav-btn.primary { border-color: var(--amber); color: var(--amber); }
  .nav-btn.primary:hover:not(:disabled) {
    background: var(--amber); color: var(--charcoal); box-shadow: 0 0 20px var(--amber-glow);
  }
  .kbd-hint { font-size: 0.65rem; color: #444; letter-spacing: 0.1em; }

  #references-section {
    position: fixed; bottom: 60px; left: 50%; transform: translateX(-50%);
    font-size: 0.65rem; color: #444; z-index: 700; text-align: center;
    max-width: 800px; pointer-events: none; line-height: 1.4; padding: 0 1rem;
  }

  /* ============ PHONES / TABLETS: hide fixed bar, rely on in-page nav ============ */
  @media (max-width: 820px) {
    .nav-row { display: none; }
    #references-section { display: none; }
    .scene { padding: 1.6rem 1rem 3rem; }
  }

  @media (max-height: 820px) {
    .act-title { font-size: clamp(1.4rem, 3vw, 2.2rem); }
    .narration { font-size: 0.9rem; line-height: 1.55; margin-bottom: 1.2rem; }
    .city-scene { height: min(28vh, 260px); }
    .science-grid, .hazard-grid, .situation-grid, .inventory-grid { gap: 0.6rem; }
    .science-card, .hazard-card, .situation-card { padding: 0.8rem 0.9rem; }
    .decision-banner { padding: 1rem 1.2rem; margin-bottom: 1.2rem; }
    .choice-btn { padding: 0.8rem 1rem; font-size: 0.9rem; }
    .checklist { gap: 0.5rem; }
    .check-item { padding: 0.7rem 0.9rem; font-size: 0.85rem; }
    .cross-section-wrap { max-width: 560px; }
    .final-message { padding: 1rem; margin-top: 1.2rem; }
  }

  @media (max-width: 600px) {
    .scene { padding: 1.4rem 0.8rem 3rem; }
    .hazard-grid, .science-grid { grid-template-columns: 1fr 1fr; }
    .start-title { font-size: 2.2rem; }
    .start-sub { letter-spacing: 0.15em; }
    .scene-nav .nav-btn { flex: 1 1 auto; min-width: 120px; }
  }
</style>
</head>
<body>

<div id="error-banner"></div>

<noscript>
  <div style="position:fixed;inset:0;background:#121212;color:#e8a33d;display:flex;align-items:center;justify-content:center;z-index:3000;padding:2rem;text-align:center;font-family:sans-serif;">
    This interactive experience requires JavaScript. Please enable JavaScript and reload the page.
  </div>
</noscript>

<div id="start-overlay">
  <div class="start-title">EARTH<span>QUAKE</span></div>
  <div class="start-sub">THE FIRST 60 SECONDS</div>
  <p class="start-desc">
    Understand the threat. Prepare. Respond.<br><br>
    An interactive cinematic simulation for the classroom.
    Tap the buttons at the bottom of each screen, or swipe left and right.
  </p>
  <button class="start-btn" id="start-btn">Start Experience</button>
  <div class="start-note">EDUCATIONAL SIMULATION &nbsp;·&nbsp; NOT A REAL EMERGENCY ALERT</div>
</div>

<div id="progress-bar"></div>
<div id="act-indicator">
  <div class="act-dot active"></div><div class="act-dot"></div>
  <div class="act-dot"></div><div class="act-dot"></div><div class="act-dot"></div>
</div>

<!-- ACT I -->
<section class="scene active" id="act1">
  <div class="scene-content">
    <div class="act-label">Act I — The Moment Before</div>
    <div class="clock-display" id="clock">10:42:59<span class="millis"> AM</span></div>
    <div class="city-scene" id="city1">
      <div class="city-skyline" id="skyline1"></div>
      <div class="city-ground"></div>
    </div>
    <p class="narration">
      A modern city, going about its morning. <strong>10:43:00 AM.</strong>
      In the next sixty seconds, the ground will move. What causes it?
      Why can it be dangerous? And what can we do to protect ourselves?
    </p>
    <div class="scene-nav">
      <button class="nav-btn primary scene-next">Next →</button>
    </div>
  </div>
</section>

<!-- ACT II -->
<section class="scene" id="act2">
  <div class="scene-content">
    <div class="act-label">Act II — Beneath the Surface</div>
    <h2 class="act-title">Why the Ground Moves</h2>
    <div class="cross-section-wrap">
      <svg viewBox="0 0 800 400" xmlns="http://www.w3.org/2000/svg">
        <rect x="0" y="0" width="800" height="120" fill="#12121a"/>
        <rect x="0" y="120" width="800" height="280" fill="#1e1e24"/>
        <rect x="0" y="120" width="800" height="40" fill="#2a2a33"/>
        <line x1="350" y1="120" x2="450" y2="400" stroke="#e8a33d" stroke-width="3" stroke-dasharray="8,6"/>
        <circle cx="400" cy="280" r="7" fill="#c0392b">
          <animate attributeName="r" values="5;9;5" dur="2s" repeatCount="indefinite"/>
        </circle>
        <text x="420" y="285" fill="#c0392b" font-size="13" font-family="Segoe UI, sans-serif">Focus (Hypocenter)</text>
        <circle cx="400" cy="120" r="5" fill="#e8a33d"/>
        <text x="420" y="115" fill="#e8a33d" font-size="13" font-family="Segoe UI, sans-serif">Epicenter</text>
        <circle cx="400" cy="280" r="40" fill="none" stroke="#e8a33d" stroke-opacity="0.4" stroke-width="1.5">
          <animate attributeName="r" values="20;120" dur="3s" repeatCount="indefinite"/>
          <animate attributeName="stroke-opacity" values="0.6;0" dur="3s" repeatCount="indefinite"/>
        </circle>
        <circle cx="400" cy="280" r="40" fill="none" stroke="#e8a33d" stroke-opacity="0.4" stroke-width="1.5">
          <animate attributeName="r" values="20;120" dur="3s" begin="1s" repeatCount="indefinite"/>
          <animate attributeName="stroke-opacity" values="0.6;0" dur="3s" begin="1s" repeatCount="indefinite"/>
        </circle>
        <circle cx="400" cy="280" r="40" fill="none" stroke="#e8a33d" stroke-opacity="0.4" stroke-width="1.5">
          <animate attributeName="r" values="20;120" dur="3s" begin="2s" repeatCount="indefinite"/>
          <animate attributeName="stroke-opacity" values="0.6;0" dur="3s" begin="2s" repeatCount="indefinite"/>
        </circle>
        <text x="30" y="145" fill="#888" font-size="12" font-family="Segoe UI, sans-serif">Crust</text>
        <text x="30" y="220" fill="#888" font-size="12" font-family="Segoe UI, sans-serif">Mantle</text>
        <text x="30" y="380" fill="#888" font-size="12" font-family="Segoe UI, sans-serif">Deep Earth</text>
        <polygon points="200,160 230,150 230,170" fill="#e8a33d" opacity="0.7"/>
        <polygon points="570,160 600,150 600,170" fill="#e8a33d" opacity="0.7"/>
        <text x="210" y="190" fill="#e8a33d" font-size="11" font-family="Segoe UI, sans-serif">Plate motion</text>
        <text x="520" y="190" fill="#e8a33d" font-size="11" font-family="Segoe UI, sans-serif">Plate motion</text>
      </svg>
    </div>
    <p class="narration">
      Earth's outer shell is broken into <strong>tectonic plates</strong>. They move slowly,
      but where they grind past each other, stress builds in the rock along a <strong>fault</strong>.
      When the stress exceeds the rock's strength, it suddenly slips. Energy radiates as
      <strong>seismic waves</strong> — and the ground shakes. The point where slip begins
      underground is the <strong>focus</strong>; the point directly above it on the surface is the
      <strong>epicenter</strong>. <span class="amber">Earthquakes can also occur far from plate
      boundaries, within plates.</span>
    </p>
    <div class="science-grid">
      <div class="science-card">
        <h4>Elastic Rebound</h4>
        <p>Rocks bend and store energy over time. When they snap, they rebound to a new position, releasing stored energy.</p>
      </div>
      <div class="science-card">
        <h4>Seismic Waves</h4>
        <p>P-waves, S-waves, and surface waves travel outward, carrying energy that shakes the ground and structures.</p>
      </div>
      <div class="science-card">
        <h4>Focus vs. Epicenter</h4>
        <p>The focus is the underground origin; the epicenter is the surface location directly above it.</p>
      </div>
    </div>
    <div class="scene-nav">
      <button class="nav-btn scene-prev">← Back</button>
      <button class="nav-btn primary scene-next">Next →</button>
    </div>
  </div>
</section>

<!-- ACT III -->
<section class="scene" id="act3">
  <div class="scene-content">
    <div class="act-label">Act III — When the Ground Moves</div>
    <h2 class="act-title">The Hazards</h2>
    <p class="narration">
      Ground shaking is the primary cause of earthquake damage. But it triggers a chain of
      secondary hazards. <span class="amber">Not every earthquake produces every hazard</span> —
      risk depends on shaking intensity, distance, ground conditions, and building design.
    </p>
    <div class="hazard-grid">
      <div class="hazard-card">
        <div class="hazard-icon">🧱</div>
        <h4>Falling Objects &amp; Debris</h4>
        <p>Most earthquake injuries come from falling debris, toppling furniture, and flying glass — not from the ground opening up.</p>
        <span class="hazard-hint">Tap to reveal</span>
      </div>
      <div class="hazard-card">
        <div class="hazard-icon">🏚️</div>
        <h4>Building &amp; Infrastructure Damage</h4>
        <p>Older or poorly designed structures may crack, shift, or collapse. Roads, bridges, and utility lines can be disrupted.</p>
        <span class="hazard-hint">Tap to reveal</span>
      </div>
      <div class="hazard-card">
        <div class="hazard-icon">〰️</div>
        <h4>Ground Rupture</h4>
        <p>Where a fault reaches the surface, the ground can crack and shift, damaging foundations and buried pipes.</p>
        <span class="hazard-hint">Tap to reveal</span>
      </div>
      <div class="hazard-card">
        <div class="hazard-icon">⛰️</div>
        <h4>Landslides</h4>
        <p>Shaking can destabilize steep slopes, sending rock and soil down onto roads and buildings.</p>
        <span class="hazard-hint">Tap to reveal</span>
      </div>
      <div class="hazard-card">
        <div class="hazard-icon">💧</div>
        <h4>Liquefaction</h4>
        <p>In water-saturated sandy soil, strong shaking can cause the ground to temporarily behave like a fluid, losing strength.</p>
        <span class="hazard-hint">Tap to reveal</span>
      </div>
      <div class="hazard-card">
        <div class="hazard-icon">🔥</div>
        <h4>Fire &amp; Utility Damage</h4>
        <p>Ruptured gas lines and damaged electrical systems can ignite fires, which may be hard to fight if water mains break.</p>
        <span class="hazard-hint">Tap to reveal</span>
      </div>
      <div class="hazard-card">
        <div class="hazard-icon">🌊</div>
        <h4>Tsunamis</h4>
        <p>Certain undersea earthquakes can displace the ocean floor and generate destructive waves. Not all undersea quakes cause tsunamis.</p>
        <span class="hazard-hint">Tap to reveal</span>
      </div>
    </div>
    <div class="scene-nav">
      <button class="nav-btn scene-prev">← Back</button>
      <button class="nav-btn primary scene-next">Next →</button>
    </div>
  </div>
</section>

<!-- ACT IV -->
<section class="scene" id="act4">
  <div class="scene-content">
    <div class="act-label">Act IV — Your Decision</div>

    <div id="decision-phase">
      <div class="decision-banner">
        <h3>THE GROUND IS SHAKING.</h3>
        <div class="question">WHAT DO YOU DO?</div>
      </div>
      <div class="choices" id="choices">
        <button class="choice-btn" data-choice="A">
          <span class="choice-letter">A</span> Run outside immediately.
        </button>
        <button class="choice-btn" data-choice="B">
          <span class="choice-letter">B</span> Stand beside a window.
        </button>
        <button class="choice-btn" data-choice="C">
          <span class="choice-letter">C</span> Drop, Cover, and Hold On.
        </button>
      </div>
      <div class="feedback-box" id="feedback"></div>
    </div>

    <div id="situation-phase" style="display:none; margin-top:2rem;">
      <h3 style="font-size:1.1rem; letter-spacing:0.08em; color:var(--amber); margin-bottom:1rem;">
        YOUR SURROUNDINGS CHANGE THE RESPONSE
      </h3>
      <div class="situation-grid">
        <div class="situation-card">
          <h4>🏢 Indoors</h4>
          <p>Stay inside. Drop to hands and knees, cover your head and neck under a sturdy table, and hold on until shaking stops. Stay away from windows.</p>
        </div>
        <div class="situation-card">
          <h4>🌳 Outdoors</h4>
          <p>Move to an open area away from buildings, trees, streetlights, and power lines — if it is safe to do so. Drop, cover, and hold on.</p>
        </div>
        <div class="situation-card">
          <h4>🚗 Driving</h4>
          <p>Pull over to a clear location away from overpasses, bridges, buildings, and power lines. Stay inside with your seatbelt fastened until the shaking stops.</p>
        </div>
      </div>

      <h3 style="font-size:1.1rem; letter-spacing:0.08em; color:var(--amber); margin:2rem 0 0.5rem;">
        PREPARE BEFORE THE NEXT ONE
      </h3>
      <p style="font-size:0.9rem; color:#aaa; margin-bottom:1rem;">
        Preparation is not a single action. It is a set of deliberate choices. Tap each item.
      </p>
      <div class="inventory-grid" id="inventory">
        <div class="inv-item" data-item="water"><div class="inv-icon">💧</div><span>Water</span></div>
        <div class="inv-item" data-item="food"><div class="inv-icon">🥫</div><span>Food</span></div>
        <div class="inv-item" data-item="flashlight"><div class="inv-icon">🔦</div><span>Flashlight</span></div>
        <div class="inv-item" data-item="firstaid"><div class="inv-icon">🩹</div><span>First Aid</span></div>
        <div class="inv-item" data-item="radio"><div class="inv-icon">📻</div><span>Radio</span></div>
        <div class="inv-item" data-item="meds"><div class="inv-icon">💊</div><span>Medications</span></div>
        <div class="inv-item" data-item="furniture"><div class="inv-icon">🔧</div><span>Secure Furniture</span></div>
        <div class="inv-item" data-item="plan"><div class="inv-icon">👨‍👩‍👧</div><span>Family Plan</span></div>
      </div>
      <div class="inv-detail" id="inv-detail"></div>
    </div>

    <div class="scene-nav">
      <button class="nav-btn scene-prev">← Back</button>
      <button class="nav-btn primary scene-next">Next →</button>
    </div>
  </div>
</section>

<!-- ACT V -->
<section class="scene" id="act5">
  <div class="scene-content">
    <div class="act-label">Act V — After the Shaking</div>
    <h2 class="act-title">Is It Over?</h2>
    <p class="narration">
      The shaking stops. The city becomes quiet. But the danger may not have passed.
      <strong>Aftershocks</strong> can occur — and damaged structures, gas leaks, and
      downed power lines remain hazardous. Emergency response is a process, not an instant fix.
    </p>
    <div class="checklist">
      <div class="check-item"><span class="check-num">1</span> Check yourself and others for injuries when it is safe to do so.</div>
      <div class="check-item"><span class="check-num">2</span> Provide first aid if able; seek emergency medical help for serious injuries.</div>
      <div class="check-item"><span class="check-num">3</span> Expect aftershocks — be ready to Drop, Cover, and Hold On again.</div>
      <div class="check-item"><span class="check-num">4</span> Avoid damaged buildings and hazardous areas.</div>
      <div class="check-item"><span class="check-num">5</span> Watch for damaged electrical lines, gas leaks, and fires — do not approach them.</div>
      <div class="check-item"><span class="check-num">6</span> Follow instructions from local emergency authorities.</div>
      <div class="check-item"><span class="check-num">7</span> Use phones responsibly — reserve emergency calls for urgent situations.</div>
      <div class="check-item"><span class="check-num">8</span> Communicate with family using your established plan.</div>
      <div class="check-item"><span class="check-num">9</span> Do not re-enter damaged structures until authorities say it is safe.</div>
    </div>
    <p class="narration" style="margin-top:1.5rem;">
      Emergency services assess damage, prioritize life-threatening emergencies, rescue people,
      treat injuries, manage hazards, and work to restore essential services. Recovery takes
      coordination, resources, and time.
    </p>

    <h3 style="font-size:1.1rem; letter-spacing:0.08em; color:var(--amber); margin:2rem 0 1rem;">
      QUICK KNOWLEDGE CHECK
    </h3>
    <div class="quiz-q" id="q1">
      <h4>1. What is the primary cause of most earthquake damage and injuries?</h4>
      <div class="quiz-options">
        <button class="quiz-opt" data-q="1" data-answer="a">A. The ground opening into deep cracks</button>
        <button class="quiz-opt" data-q="1" data-answer="b">B. Ground shaking and falling debris</button>
        <button class="quiz-opt" data-q="1" data-answer="c">C. Volcanic eruptions</button>
      </div>
    </div>
    <div class="quiz-q" id="q2">
      <h4>2. What should you do if you are indoors during an earthquake?</h4>
      <div class="quiz-options">
        <button class="quiz-opt" data-q="2" data-answer="a">A. Run outside immediately</button>
        <button class="quiz-opt" data-q="2" data-answer="b">B. Stand in a doorway</button>
        <button class="quiz-opt" data-q="2" data-answer="c">C. Drop, Cover, and Hold On</button>
      </div>
    </div>
    <div class="quiz-q" id="q3">
      <h4>3. After the shaking stops, which of the following is recommended?</h4>
      <div class="quiz-options">
        <button class="quiz-opt" data-q="3" data-answer="a">A. Re-enter damaged buildings to retrieve belongings</button>
        <button class="quiz-opt" data-q="3" data-answer="b">B. Expect aftershocks and avoid damaged structures</button>
        <button class="quiz-opt" data-q="3" data-answer="c">C. Use matches to check for gas leaks</button>
      </div>
    </div>
    <div class="quiz-result" id="quiz-result"></div>

    <div class="final-message">
      <h2>
        YOU CANNOT PREVENT AN EARTHQUAKE.<br>
        <span>BUT YOU CAN PREPARE FOR ONE.</span>
      </h2>
      <button class="start-btn" id="replay-btn" style="margin-top:2rem;">Replay Experience</button>
    </div>

    <div class="scene-nav">
      <button class="nav-btn scene-prev">← Back</button>
    </div>
  </div>
</section>

<!-- FIXED NAV (desktop only) -->
<div class="nav-row">
  <button class="nav-btn" id="prev-btn">← Back</button>
  <span class="kbd-hint">← → arrow keys · swipe</span>
  <button class="nav-btn primary" id="next-btn">Next →</button>
</div>

<div id="references-section">
  Sources: USGS Earthquake Hazards Program; Ready.gov; FEMA; CDC; Great ShakeOut.
  This is a fictional educational simulation. Follow local emergency guidance.
</div>

<script>
(function () {
  'use strict';

  function reportError(msg) {
    var banner = document.getElementById('error-banner');
    if (!banner) return;
    banner.textContent = msg;
    banner.classList.add('show');
  }
  window.addEventListener('error', function (e) {
    reportError('Script error: ' + (e.message || 'unknown') + ' — check the browser console.');
  });

  var currentScene = 0;
  var scenes, totalScenes, progressBar, dots, prevBtn, nextBtn;
  var decisionMade = false;
  var quizAnswers = {};
  var clockTimer = null;
  var started = false;

  var INV_TEXT = {
    water: 'Water: At least 1 gallon per person per day for several days. Store in a cool, dark place.',
    food: 'Nonperishable food: Choose items that need no cooking, and include a manual can opener.',
    flashlight: 'Flashlight and extra batteries: Avoid candles — they are a fire risk after an earthquake.',
    firstaid: 'First-aid kit: Include bandages, antiseptics, and any personal medications you need.',
    radio: 'Battery-powered or hand-crank radio: A reliable way to receive official emergency information when power is out.',
    meds: 'Essential medications: Keep an adequate supply and rotate it before it expires.',
    furniture: 'Secure heavy furniture: Anchor bookcases, shelves, and water heaters to walls. Move heavy objects to lower shelves.',
    plan: 'Family plan: Agree on a meeting place and an out-of-area contact. Practice Drop, Cover, and Hold On together.'
  };

  var QUIZ_CORRECT = { 1: 'b', 2: 'c', 3: 'b' };

  function init() {
    scenes = document.querySelectorAll('.scene');
    totalScenes = scenes.length;
    progressBar = document.getElementById('progress-bar');
    dots = document.querySelectorAll('.act-dot');
    prevBtn = document.getElementById('prev-btn');
    nextBtn = document.getElementById('next-btn');

    document.getElementById('start-btn').addEventListener('click', startExperience);
    document.getElementById('replay-btn').addEventListener('click', replay);
    prevBtn.addEventListener('click', prevScene);
    nextBtn.addEventListener('click', nextScene);

    document.addEventListener('click', handleDelegatedClick);
    document.addEventListener('keydown', handleKeydown);
    setupSwipe();

    showScene(0);
  }

  function handleDelegatedClick(e) {
    var t = e.target;
    if (t.closest && t.closest('.scene-next')) { nextScene(); return; }
    if (t.closest && t.closest('.scene-prev')) { prevScene(); return; }

    var hazard = t.closest ? t.closest('.hazard-card') : null;
    if (hazard) { toggleHazard(hazard); return; }

    var choice = t.closest ? t.closest('#choices .choice-btn') : null;
    if (choice && !choice.disabled) { selectDecision(choice.getAttribute('data-choice')); return; }

    var inv = t.closest ? t.closest('.inv-item') : null;
    if (inv) { showInv(inv, inv.getAttribute('data-item')); return; }

    var opt = t.closest ? t.closest('.quiz-opt') : null;
    if (opt && !opt.disabled) {
      answerQuiz(parseInt(opt.getAttribute('data-q'), 10), opt.getAttribute('data-answer'), opt);
    }
  }

  function startExperience() {
    started = true;
    document.getElementById('start-overlay').classList.add('hidden');
    buildSkyline();
    animateClock();
    window.setTimeout(function () {
      var city = document.getElementById('city1');
      if (city) city.classList.add('shaking');
    }, 3000);
  }

  function animateClock() {
    var clock = document.getElementById('clock');
    if (!clock) return;
    if (clockTimer) { clearInterval(clockTimer); clockTimer = null; }
    var seconds = 59;
    clockTimer = setInterval(function () {
      seconds++;
      if (seconds >= 60) {
        clock.innerHTML = '10:43:00<span class="millis"> AM</span>';
        clearInterval(clockTimer);
        clockTimer = null;
      }
    }, 1000);
  }

  function buildSkyline() {
    var skyline = document.getElementById('skyline1');
    if (!skyline) return;
    skyline.innerHTML = '';
    var heights = [40, 65, 30, 80, 50, 90, 35, 70, 45, 60, 55, 75, 38, 85, 42, 68, 52, 78, 33, 62];
    heights.forEach(function (h) {
      var b = document.createElement('div');
      b.className = 'building';
      b.style.height = h + '%';
      b.style.width = (8 + Math.random() * 12) + 'px';
      skyline.appendChild(b);
    });
  }

  function showScene(index) {
    if (index < 0 || index >= totalScenes) return;
    for (var i = 0; i < scenes.length; i++) {
      scenes[i].classList.toggle('active', i === index);
      var p = scenes[i].querySelector('.scene-prev');
      var n = scenes[i].querySelector('.scene-next');
      if (p) p.style.display = (i === 0) ? 'none' : '';
      if (n) n.style.display = (i === totalScenes - 1) ? 'none' : '';
    }
    currentScene = index;
    progressBar.style.width = ((index + 1) / totalScenes * 100) + '%';
    for (var d = 0; d < dots.length; d++) {
      dots[d].classList.toggle('active', d === index);
    }
    prevBtn.disabled = (index === 0);
    nextBtn.disabled = (index === totalScenes - 1);
    nextBtn.textContent = (index === totalScenes - 1) ? 'End' : 'Next →';

    var active = scenes[index];
    if (active) active.scrollTop = 0;
    window.scrollTo(0, 0);
  }

  function nextScene() { if (currentScene < totalScenes - 1) showScene(currentScene + 1); }
  function prevScene() { if (currentScene > 0) showScene(currentScene - 1); }

  function handleKeydown(e) {
    if (!started) return;
    var tag = e.target && e.target.tagName;
    if (tag === 'BUTTON' && (e.key === 'Enter' || e.key === ' ')) return;
    if (e.key === 'ArrowRight' || e.key === ' ') { e.preventDefault(); nextScene(); }
    else if (e.key === 'ArrowLeft') { e.preventDefault(); prevScene(); }
  }

  function setupSwipe() {
    var startX = null, startY = null;
    document.addEventListener('touchstart', function (e) {
      if (e.touches.length !== 1) { startX = null; return; }
      startX = e.touches[0].clientX;
      startY = e.touches[0].clientY;
    }, { passive: true });

    document.addEventListener('touchend', function (e) {
      if (startX === null || !started) { startX = null; return; }
      var dx = e.changedTouches[0].clientX - startX;
      var dy = e.changedTouches[0].clientY - startY;
      startX = null; startY = null;
      if (Math.abs(dx) < 70) return;
      if (Math.abs(dx) < Math.abs(dy) * 1.5) return;
      if (dx < 0) nextScene(); else prevScene();
    }, { passive: true });
  }

  function toggleHazard(el) {
    var wasExpanded = el.classList.contains('expanded');
    var all = document.querySelectorAll('.hazard-card');
    for (var i = 0; i < all.length; i++) all[i].classList.remove('expanded');
    if (!wasExpanded) el.classList.add('expanded');
  }

  function selectDecision(choice) {
    if (decisionMade) return;
    decisionMade = true;

    var feedback = document.getElementById('feedback');
    var buttons = document.querySelectorAll('#choices .choice-btn');
    for (var i = 0; i < buttons.length; i++) buttons[i].disabled = true;

    var correctBtn = document.querySelector('#choices .choice-btn[data-choice="C"]');
    if (correctBtn) correctBtn.classList.add('correct');

    if (choice === 'C') {
      feedback.className = 'feedback-box success show';
      feedback.innerHTML = '<strong>Correct.</strong> Drop to your hands and knees. Cover your head and neck under a sturdy table or desk if available. Hold on until the shaking stops. Do not run outside during active shaking — falling debris is a major injury risk.';
    } else if (choice === 'A') {
      feedback.className = 'feedback-box danger show';
      feedback.innerHTML = '<strong>Not the safest choice.</strong> Running outside during active shaking exposes you to falling glass, debris, and collapsing walls. Stay inside and Drop, Cover, and Hold On. The correct answer is C.';
      document.querySelector('#choices .choice-btn[data-choice="A"]').classList.add('wrong');
    } else if (choice === 'B') {
      feedback.className = 'feedback-box danger show';
      feedback.innerHTML = '<strong>Not the safest choice.</strong> Windows can shatter during shaking. Stay away from glass and exterior walls. Drop, Cover, and Hold On under sturdy furniture. The correct answer is C.';
      document.querySelector('#choices .choice-btn[data-choice="B"]').classList.add('wrong');
    }

    window.setTimeout(function () {
      document.getElementById('situation-phase').style.display = 'block';
    }, 1200);
  }

  function showInv(el, key) {
    var items = document.querySelectorAll('.inv-item');
    for (var i = 0; i < items.length; i++) items[i].classList.remove('active');
    el.classList.add('active');
    var detail = document.getElementById('inv-detail');
    detail.textContent = INV_TEXT[key] || '';
    detail.classList.add('show');
  }

  function answerQuiz(qNum, answer, btn) {
    var q = document.getElementById('q' + qNum);
    if (!q || q.dataset.answered) return;
    q.dataset.answered = 'true';

    var options = q.querySelectorAll('.quiz-opt');
    for (var i = 0; i < options.length; i++) options[i].disabled = true;

    var correct = QUIZ_CORRECT[qNum];
    var isCorrect = (answer === correct);

    btn.classList.add(isCorrect ? 'correct-answer' : 'wrong-answer');
    if (!isCorrect) {
      for (var j = 0; j < options.length; j++) {
        if (options[j].getAttribute('data-answer') === correct) options[j].classList.add('correct-answer');
      }
    }

    quizAnswers[qNum] = isCorrect;
    checkQuizComplete();
  }

  function checkQuizComplete() {
    if (Object.keys(quizAnswers).length === 3) {
      var result = document.getElementById('quiz-result');
      var correctCount = 0;
      for (var k in quizAnswers) { if (quizAnswers[k]) correctCount++; }
      if (correctCount === 3) {
        result.className = 'quiz-result show good';
        result.innerHTML = 'Excellent — 3 out of 3. You understand the key actions: Drop, Cover, and Hold On, and prepare before the next event.';
      } else {
        result.className = 'quiz-result show mixed';
        result.innerHTML = 'You answered ' + correctCount + ' out of 3 correctly. Review the key actions: Drop, Cover, and Hold On; expect aftershocks; avoid damaged structures.';
      }
    }
  }

  function replay() {
    decisionMade = false;
    quizAnswers = {};

    var feedback = document.getElementById('feedback');
    feedback.className = 'feedback-box';
    feedback.innerHTML = '';
    document.getElementById('situation-phase').style.display = 'none';

    var buttons = document.querySelectorAll('#choices .choice-btn');
    for (var i = 0; i < buttons.length; i++) {
      buttons[i].disabled = false;
      buttons[i].classList.remove('correct', 'wrong');
    }

    var invItems = document.querySelectorAll('.inv-item');
    for (var j = 0; j < invItems.length; j++) invItems[j].classList.remove('active');

    var detail = document.getElementById('inv-detail');
    detail.classList.remove('show');
    detail.textContent = '';

    [1, 2, 3].forEach(function (n) {
      var q = document.getElementById('q' + n);
      q.dataset.answered = '';
      var opts = q.querySelectorAll('.quiz-opt');
      for (var m = 0; m < opts.length; m++) {
        opts[m].disabled = false;
        opts[m].classList.remove('correct-answer', 'wrong-answer');
      }
    });

    var result = document.getElementById('quiz-result');
    result.className = 'quiz-result';
    result.innerHTML = '';

    var hazards = document.querySelectorAll('.hazard-card');
    for (var h = 0; h < hazards.length; h++) hazards[h].classList.remove('expanded');

    if (clockTimer) { clearInterval(clockTimer); clockTimer = null; }
    document.getElementById('city1').classList.remove('shaking');
    document.getElementById('clock').innerHTML = '10:42:59<span class="millis"> AM</span>';

    started = false;
    showScene(0);
    document.getElementById('start-overlay').classList.remove('hidden');
  }

  if (document.readyState === 'loading') document.addEventListener('DOMContentLoaded', init);
  else init();
})();
</script>
</body>
</html>