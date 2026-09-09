# Ex05 Image Carousel
## Date: 07-09-2026

## AIM
To create a Image Carousel using React 

## ALGORITHM
### STEP 1 Initial Setup:
Input: A list of images to display in the carousel.

Output: A component displaying the images with navigation controls (e.g., next/previous buttons).

### Step 2 State Management:
Use a state variable (currentIndex) to track the index of the current image displayed.

The carousel starts with the first image, so initialize currentIndex to 0.

### Step 3 Navigation Controls:
Next Image: When the "Next" button is clicked, increment currentIndex.

If currentIndex is at the end of the image list (last image), loop back to the first image using modulo:
currentIndex = (currentIndex + 1) % images.length;

Previous Image: When the "Previous" button is clicked, decrement currentIndex.

If currentIndex is at the beginning (first image), loop back to the last image:
currentIndex = (currentIndex - 1 + images.length) % images.length;

### Step 4 Displaying the Image:
The currentIndex determines which image is displayed.

Using the currentIndex, display the corresponding image from the images list.

### Step 5 Auto-Rotation:
Set an interval to automatically change the image after a set amount of time (e.g., 3 seconds).

Use setInterval to call the nextImage() function at regular intervals.

Clean up the interval when the component unmounts using clearInterval to prevent memory leaks.

## PROGRAM

## HTML
```
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>Frame &amp; Flicker — A Living Film Reel</title>
<meta name="description" content="An artistic, hand-built image carousel — a film-reel gallery with light, motion and a few secrets." />
<link rel="stylesheet" href="style.css" />
</head>
<body>

  <!-- Ambient light / firefly particle field -->
  <canvas id="particle-field"></canvas>

  <!-- Film grain overlay -->
  <div class="grain"></div>

  <!-- Falling confetti layer for easter-egg celebrations -->
  <div id="confetti-layer" aria-hidden="true"></div>

  <div class="scene">

    <header class="marquee">
      <button id="logo-reel" class="reel-logo" title="???" aria-label="Secret reel logo">
        <svg viewBox="0 0 64 64" width="42" height="42">
          <circle cx="32" cy="32" r="30" fill="none" stroke="currentColor" stroke-width="3"/>
          <circle cx="32" cy="32" r="6" fill="currentColor"/>
          <circle cx="32" cy="12" r="6" fill="currentColor"/>
          <circle cx="50" cy="24" r="6" fill="currentColor"/>
          <circle cx="50" cy="42" r="6" fill="currentColor"/>
          <circle cx="14" cy="42" r="6" fill="currentColor"/>
          <circle cx="14" cy="24" r="6" fill="currentColor"/>
        </svg>
      </button>
      <div class="title-block">
        <h1>Frame <span class="amp">&amp;</span> Flicker</h1>
        <p class="subtitle">a living reel — <span id="live-clock"></span></p>
      </div>
      <button id="fullscreen-btn" class="ghost-btn" title="Toggle fullscreen lightbox (F)">⤢</button>
    </header>

    <main class="stage-wrap">

      <div class="sprocket sprocket-top" aria-hidden="true"></div>

      <div class="stage" id="stage" tabindex="0" aria-label="Image carousel, use arrow keys to navigate">
        <div class="spotlight" id="spotlight"></div>

        <div class="frame" id="frame">
          <!-- Slides are injected here by script.js -->
        </div>

        <button class="nav-btn prev" id="prev-btn" aria-label="Previous image">
          <span class="blade">◀</span>
        </button>
        <button class="nav-btn next" id="next-btn" aria-label="Next image">
          <span class="blade">▶</span>
        </button>

        <div class="clapper">
          <span id="counter-current">01</span>
          <span class="slash">/</span>
          <span id="counter-total">10</span>
        </div>

        <button class="play-toggle" id="play-toggle" title="Pause autoplay (Space)">
          <svg id="progress-ring" width="46" height="46" viewBox="0 0 46 46">
            <circle class="ring-track" cx="23" cy="23" r="20"/>
            <circle class="ring-progress" id="ring-progress" cx="23" cy="23" r="20"/>
          </svg>
          <span id="play-icon">❙❙</span>
        </button>
      </div>

      <div class="sprocket sprocket-bottom" aria-hidden="true"></div>

      <div class="caption-card">
        <p id="caption-text" class="caption-text"></p>
      </div>

      <div class="thumbnail-dock" id="thumbnail-dock" aria-label="Jump to a specific frame">
        <!-- Thumbnails injected by script.js -->
      </div>

      <div class="hint-row">
        <span>drag / swipe</span><span>•</span><span>← → keys</span><span>•</span><span>space to pause</span><span>•</span><span class="secret-hint">psst… try the arrows and letters from an old cartridge game</span>
      </div>
    </main>

    <div id="toast" class="toast" role="status" aria-live="polite"></div>

    <footer class="credit-plaque">
      <div class="plaque-inner">
        <span class="reel-icon">🎞</span>
        <span>Made by <strong>Vishal S</strong></span>
        <span class="dot">•</span>
        <span>Reg. No: <strong>212225220120</strong></span>
      </div>
    </footer>
  </div>

  <!-- Fullscreen lightbox -->
  <div id="lightbox" class="lightbox" aria-hidden="true">
    <img id="lightbox-img" src="" alt="" />
    <button id="lightbox-close" class="ghost-btn lightbox-close">✕</button>
  </div>

  <script src="script.js"></script>
</body>
</html>

```
## CSS
```
/* ===========================================================
   Frame & Flicker — an artistic film-reel image carousel
   Vanilla HTML / CSS / JS only.
   =========================================================== */

@import url('https://fonts.googleapis.com/css2?family=Playfair+Display:ital,wght@0,600;0,700;1,600&family=Space+Mono:wght@400;700&display=swap');

:root{
  --bg-0:#0a0b10;
  --bg-1:#12131c;
  --bg-2:#1a1c28;
  --gold:#e8b34b;
  --gold-soft:#f4d597;
  --ember:#ff6a3d;
  --cream:#f4efe4;
  --muted:#8f93a8;
  --frame-w: min(78vw, 760px);
  --frame-h: min(52vw, 480px);
  --radius: 14px;
  --shadow-deep: 0 30px 70px -20px rgba(0,0,0,.75);
  font-size: 16px;
}

*,*::before,*::after{ box-sizing:border-box; }

html,body{
  height:100%;
  margin:0;
  overflow-x:hidden;
}

body{
  background:
    radial-gradient(ellipse at 20% -10%, #2a2440 0%, transparent 55%),
    radial-gradient(ellipse at 90% 110%, #201d33 0%, transparent 55%),
    linear-gradient(180deg, var(--bg-0), var(--bg-1) 40%, var(--bg-0));
  color: var(--cream);
  font-family: 'Space Mono', monospace;
  min-height:100vh;
  display:flex;
  align-items:center;
  justify-content:center;
  position:relative;
  cursor: default;
  user-select:none;
}

#particle-field{
  position:fixed;
  inset:0;
  width:100%;
  height:100%;
  z-index:0;
  pointer-events:none;
}

/* subtle animated film-grain overlay */
.grain{
  position:fixed;
  inset:-100px;
  z-index:1;
  pointer-events:none;
  opacity:.045;
  mix-blend-mode:overlay;
  background-image:url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='120' height='120'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='2' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)'/%3E%3C/svg%3E");
  animation: grain-shift 1.4s steps(4) infinite;
}
@keyframes grain-shift{
  0%{ transform:translate(0,0); }
  25%{ transform:translate(-2%,2%); }
  50%{ transform:translate(2%,-2%); }
  75%{ transform:translate(-1%,-1%); }
  100%{ transform:translate(0,0); }
}

#confetti-layer{
  position:fixed; inset:0; z-index:60; pointer-events:none; overflow:hidden;
}
.confetti-piece{
  position:absolute; top:-20px; border-radius:2px; opacity:.9;
  animation: confetti-fall linear forwards;
}
@keyframes confetti-fall{
  to{ transform: translateY(110vh) rotate(720deg); opacity:.2; }
}

.scene{
  position:relative;
  z-index:2;
  width:100%;
  max-width:960px;
  padding: 28px 18px 18px;
  display:flex;
  flex-direction:column;
  align-items:center;
  gap: 14px;
}

/* ---------------- Header ---------------- */
.marquee{
  width:100%;
  display:flex;
  align-items:center;
  justify-content:space-between;
  gap:16px;
}

.reel-logo{
  background:none;
  border: 1px solid rgba(232,179,75,.35);
  color: var(--gold);
  border-radius:50%;
  width:52px; height:52px;
  display:flex; align-items:center; justify-content:center;
  cursor:pointer;
  transition: transform .5s ease, box-shadow .3s ease, color .3s ease;
}
.reel-logo:hover{
  color: var(--gold-soft);
  box-shadow: 0 0 25px rgba(232,179,75,.35);
}
.reel-logo.spin{ animation: reel-spin 1.1s cubic-bezier(.4,1.6,.4,1); }
@keyframes reel-spin{ to{ transform: rotate(360deg); } }

.title-block{ text-align:center; flex:1; }
.title-block h1{
  font-family:'Playfair Display', serif;
  font-weight:700;
  font-size: clamp(1.6rem, 4vw, 2.5rem);
  margin:0;
  letter-spacing:.5px;
  background: linear-gradient(95deg, var(--gold-soft), var(--gold) 45%, var(--ember));
  -webkit-background-clip:text;
  background-clip:text;
  color:transparent;
  text-shadow: 0 0 40px rgba(232,179,75,.15);
}
.title-block .amp{ font-style:italic; opacity:.8; }
.subtitle{
  margin:2px 0 0;
  font-size:.72rem;
  letter-spacing:3px;
  text-transform:uppercase;
  color: var(--muted);
}
#live-clock{ color: var(--gold); }

.ghost-btn{
  background:none;
  border:1px solid rgba(244,239,228,.2);
  color: var(--cream);
  width:44px;height:44px;
  border-radius:50%;
  cursor:pointer;
  font-size:1.1rem;
  transition: all .25s ease;
}
.ghost-btn:hover{
  border-color: var(--gold);
  color: var(--gold);
  transform: scale(1.08);
}

/* ---------------- Stage ---------------- */
.stage-wrap{
  width:100%;
  display:flex;
  flex-direction:column;
  align-items:center;
  gap:0;
}

.sprocket{
  width: var(--frame-w);
  max-width:100%;
  height:14px;
  background-image: repeating-linear-gradient(90deg, transparent 0 10px, rgba(244,239,228,.18) 10px 22px, transparent 22px 32px);
  opacity:.7;
}

.stage{
  position:relative;
  width: var(--frame-w);
  max-width:100%;
  height: var(--frame-h);
  margin: 6px 0;
  outline:none;
}

.spotlight{
  position:absolute;
  inset:-60px;
  z-index:0;
  background: radial-gradient(circle at 50% 40%, rgba(232,179,75,.16), transparent 60%);
  filter: blur(10px);
  pointer-events:none;
  transition: opacity .6s ease;
}

.frame{
  position:relative;
  width:100%; height:100%;
  border-radius: var(--radius);
  overflow:hidden;
  background: #05060a;
  box-shadow: var(--shadow-deep), 0 0 0 1px rgba(244,239,228,.08), 0 0 0 8px #14151f, 0 0 0 9px rgba(232,179,75,.25);
  perspective: 1400px;
  z-index:1;
}

.slide{
  position:absolute;
  inset:0;
  display:flex;
  align-items:center;
  justify-content:center;
  opacity:0;
  transform: scale(1.06) rotateY(6deg);
  transition: opacity .85s cubic-bezier(.22,.61,.36,1), transform 1.1s cubic-bezier(.22,.61,.36,1), filter .85s ease;
  filter: saturate(.7) brightness(.7);
  will-change: transform, opacity;
}
.slide.active{
  opacity:1;
  transform: scale(1) rotateY(0deg);
  filter: saturate(1) brightness(1);
  z-index:2;
}
.slide.exit-left{ transform: scale(.92) rotateY(-10deg) translateX(-4%); }
.slide.exit-right{ transform: scale(.92) rotateY(10deg) translateX(4%); }

.slide img{
  width:100%; height:100%;
  object-fit:cover;
  display:block;
  pointer-events:none;
  filter: contrast(1.04) saturate(1.05);
}

.slide .sheen{
  position:absolute; inset:0;
  background: linear-gradient(120deg, transparent 30%, rgba(255,255,255,.18) 45%, transparent 60%);
  transform: translateX(-120%);
  pointer-events:none;
}
.slide.active .sheen{
  animation: sheen-sweep 1.4s ease-out .15s 1;
}
@keyframes sheen-sweep{
  to{ transform: translateX(120%); }
}

.slide .vignette{
  position:absolute; inset:0;
  box-shadow: inset 0 0 90px 18px rgba(0,0,0,.55);
  pointer-events:none;
}

/* nav buttons */
.nav-btn{
  position:absolute;
  top:50%;
  transform: translateY(-50%);
  z-index:5;
  width:52px; height:52px;
  border-radius:50%;
  border:1px solid rgba(244,239,228,.25);
  background: rgba(10,11,16,.45);
  backdrop-filter: blur(6px);
  color: var(--cream);
  font-size:1.1rem;
  cursor:pointer;
  transition: all .25s ease;
  display:flex; align-items:center; justify-content:center;
}
.nav-btn:hover{
  background: var(--gold);
  color:#151220;
  border-color: var(--gold);
  box-shadow: 0 0 30px rgba(232,179,75,.5);
}
.nav-btn:active{ transform: translateY(-50%) scale(.88); }
.nav-btn.prev{ left:-4px; }
.nav-btn.next{ right:-4px; }
.nav-btn .blade{ transition: transform .25s ease; display:inline-block; }
.nav-btn.prev:hover .blade{ transform: translateX(-3px); }
.nav-btn.next:hover .blade{ transform: translateX(3px); }

@media (min-width:600px){
  .nav-btn.prev{ left:-26px; }
  .nav-btn.next{ right:-26px; }
}

.clapper{
  position:absolute;
  top:14px; left:14px;
  z-index:5;
  background: rgba(5,6,10,.55);
  border:1px solid rgba(232,179,75,.3);
  color: var(--gold-soft);
  padding:5px 10px;
  border-radius:20px;
  font-size:.78rem;
  letter-spacing:2px;
  backdrop-filter: blur(4px);
}
.clapper .slash{ opacity:.5; margin:0 2px; }

.play-toggle{
  position:absolute;
  bottom:14px; right:14px;
  z-index:5;
  width:46px; height:46px;
  border-radius:50%;
  border:none;
  background:transparent;
  cursor:pointer;
  color: var(--cream);
  display:flex; align-items:center; justify-content:center;
}
.play-toggle #play-icon{
  position:absolute;
  font-size:.68rem;
  letter-spacing:1px;
}
.ring-track{
  fill:none; stroke: rgba(244,239,228,.18); stroke-width:3;
}
.ring-progress{
  fill:none; stroke: var(--gold); stroke-width:3;
  stroke-linecap:round;
  transform: rotate(-90deg);
  transform-origin:50% 50%;
  stroke-dasharray: 125.6;
  stroke-dashoffset: 125.6;
  transition: stroke-dashoffset .1s linear;
}

/* ---------------- Caption ---------------- */
.caption-card{
  min-height: 30px;
  text-align:center;
}
.caption-text{
  margin:0;
  font-family:'Playfair Display', serif;
  font-style:italic;
  font-size: clamp(.95rem, 2vw, 1.15rem);
  color: var(--muted);
  opacity:0;
  transform: translateY(6px);
  animation: caption-in .7s ease forwards .2s;
}
@keyframes caption-in{
  to{ opacity:1; transform:translateY(0); }
}

/* ---------------- Thumbnails ---------------- */
.thumbnail-dock{
  display:flex;
  gap:8px;
  padding: 8px 6px;
  max-width:100%;
  overflow-x:auto;
  scrollbar-width: thin;
}
.thumbnail-dock::-webkit-scrollbar{ height:5px; }
.thumbnail-dock::-webkit-scrollbar-thumb{ background: rgba(232,179,75,.4); border-radius:4px; }

.thumb{
  flex: 0 0 auto;
  width:58px; height:40px;
  border-radius:6px;
  overflow:hidden;
  border:2px solid transparent;
  cursor:pointer;
  opacity:.5;
  filter: grayscale(.5);
  transition: all .3s ease;
  position:relative;
}
.thumb img{ width:100%; height:100%; object-fit:cover; display:block; }
.thumb:hover{ opacity:.85; transform: translateY(-3px); }
.thumb.active{
  opacity:1;
  filter:none;
  border-color: var(--gold);
  box-shadow: 0 4px 16px rgba(232,179,75,.35);
  transform: translateY(-4px);
}

/* ---------------- Hints ---------------- */
.hint-row{
  display:flex;
  flex-wrap:wrap;
  justify-content:center;
  gap:8px;
  font-size:.68rem;
  letter-spacing:1px;
  color: var(--muted);
  opacity:.65;
  text-transform:uppercase;
}
.secret-hint{ opacity:.4; font-style:italic; text-transform:none; letter-spacing:0; }

/* ---------------- Toast ---------------- */
.toast{
  position:fixed;
  bottom: 90px;
  left:50%;
  transform: translateX(-50%) translateY(20px);
  background: rgba(15,14,22,.92);
  border:1px solid rgba(232,179,75,.4);
  color: var(--gold-soft);
  padding:10px 18px;
  border-radius:30px;
  font-size:.8rem;
  letter-spacing:1px;
  opacity:0;
  pointer-events:none;
  transition: opacity .4s ease, transform .4s ease;
  z-index:50;
  white-space:nowrap;
}
.toast.show{
  opacity:1;
  transform: translateX(-50%) translateY(0);
}

/* ---------------- Footer credit ---------------- */
.credit-plaque{
  margin-top:6px;
  width:100%;
  display:flex;
  justify-content:center;
}
.plaque-inner{
display:flex;
  align-items:center;
  gap:12px;
  flex-wrap:wrap;
  justify-content:center;
  padding:10px 22px;
  border-radius:30px;
  border:1px solid rgba(244,239,228,.12);
  background: rgba(255,255,255,.02);
  font-size: 17px;
  letter-spacing:1px;
  color: var(--muted);
}
.plaque-inner strong{ color: var(--gold-soft); }
.plaque-inner .dot{ opacity:.4; }
.reel-icon{ filter: grayscale(.2); }

/* ---------------- Lightbox ---------------- */
.lightbox{
  position:fixed; inset:0;
  z-index:100;
  background: rgba(4,4,8,.94);
  display:flex; align-items:center; justify-content:center;
  opacity:0; visibility:hidden;
  transition: opacity .35s ease;
}
.lightbox.open{ opacity:1; visibility:visible; }
.lightbox img{
  max-width:92vw; max-height:88vh;
  border-radius:10px;
  box-shadow: 0 40px 100px rgba(0,0,0,.6);
  transform: scale(.94);
  transition: transform .35s ease;
}
.lightbox.open img{ transform: scale(1); }
.lightbox-close{
  position:absolute; top:22px; right:22px;
}

/* ---------------- Responsive ---------------- */
@media (max-width:640px){
  :root{ --frame-h: 62vw; }
  .marquee{ gap:8px; }
  .nav-btn{ width:42px; height:42px; }
  .clapper{ font-size:.65rem; padding:4px 8px; }
}

/* Reduced motion respect */
@media (prefers-reduced-motion: reduce){
  .grain, .slide, .reel-logo, .caption-text{ animation:none !important; transition:none !important; }
}

```

## JS
```
/* ===========================================================
   Frame & Flicker — carousel logic
   Built with the classic algorithm:
     1. Initial setup   -> images array + render
     2. State            -> currentIndex
     3. Navigation       -> next()/prev() with modulo wraparound
     4. Display          -> renderSlide(currentIndex)
     5. Auto-rotation    -> setInterval / clearInterval
   Plus: particles, easter eggs, thumbnails, lightbox, konami code.
   =========================================================== */

(() => {
  'use strict';

  /* ---------- STEP 1: Initial Setup — the image data ---------- */
  // 10 images, car1.jpg ... car10.jpg (drop the files into ./images/)
  const images = [
    { src: 'images/car1.jpg',  caption: 'Frame I — the opening shot.' },
    { src: 'images/car2.jpg',  caption: 'Frame II — a second glance.' },
    { src: 'images/car3.jpg',  caption: 'Frame III — chasing the light.' },
    { src: 'images/car4.jpg',  caption: 'Frame IV — held for a beat.' },
    { src: 'images/car5.jpg',  caption: 'Frame V — the quiet middle.' },
    { src: 'images/car6.jpg',  caption: 'Frame VI — a turn of the reel.' },
    { src: 'images/car7.jpg',  caption: 'Frame VII — colour deepens.' },
    { src: 'images/car8.jpg',  caption: 'Frame VIII — almost there.' },
    { src: 'images/car9.jpg',  caption: 'Frame IX — one more look.' },
    { src: 'images/car10.jpg', caption: 'Frame X — and the reel loops on.' },
  ];

  /* ---------- STEP 2: State Management ---------- */
  let currentIndex = 0;          // carousel always starts on the first image
  let previousIndex = 0;
  let autoplayTimer = null;
  const AUTOPLAY_MS = 3000;      // 3 seconds, per the algorithm
  let isPlaying = true;
  let ringRAF = null;
  let ringStart = 0;
  const RING_CIRCUMFERENCE = 125.6; // matches stroke-dasharray in CSS

  /* ---------- DOM refs ---------- */
  const frame = document.getElementById('frame');
  const thumbDock = document.getElementById('thumbnail-dock');
  const counterCurrent = document.getElementById('counter-current');
  const counterTotal = document.getElementById('counter-total');
  const captionText = document.getElementById('caption-text');
  const prevBtn = document.getElementById('prev-btn');
  const nextBtn = document.getElementById('next-btn');
  const playToggle = document.getElementById('play-toggle');
  const playIcon = document.getElementById('play-icon');
  const ringProgress = document.getElementById('ring-progress');
  const stage = document.getElementById('stage');
  const toast = document.getElementById('toast');
  const reelLogo = document.getElementById('logo-reel');
  const fullscreenBtn = document.getElementById('fullscreen-btn');
  const lightbox = document.getElementById('lightbox');
  const lightboxImg = document.getElementById('lightbox-img');
  const lightboxClose = document.getElementById('lightbox-close');
  const liveClock = document.getElementById('live-clock');
  const confettiLayer = document.getElementById('confetti-layer');
  const particleCanvas = document.getElementById('particle-field');

  counterTotal.textContent = String(images.length).padStart(2, '0');

  /* ---------- One-time DOM construction: slides & thumbnails ---------- */
  function buildSlides() {
    images.forEach((img, i) => {
      const slide = document.createElement('div');
      slide.className = 'slide';
      slide.dataset.index = String(i);

      const imageEl = document.createElement('img');
      imageEl.src = img.src;
      imageEl.alt = img.caption || `Frame ${i + 1}`;
      imageEl.draggable = false;

      const sheen = document.createElement('div');
      sheen.className = 'sheen';

      const vignette = document.createElement('div');
      vignette.className = 'vignette';

      slide.append(imageEl, sheen, vignette);
      frame.appendChild(slide);
    });
  }

  function buildThumbnails() {
    images.forEach((img, i) => {
      const thumb = document.createElement('button');
      thumb.type = 'button';
      thumb.className = 'thumb';
      thumb.setAttribute('aria-label', `Go to frame ${i + 1}`);
      thumb.dataset.index = String(i);

      const t = document.createElement('img');
      t.src = img.src;
      t.alt = '';
      t.draggable = false;

      thumb.appendChild(t);
      thumb.addEventListener('click', () => {
        const direction = i === currentIndex ? null : (i > currentIndex ? 'forward' : 'backward');
        goTo(i, direction);
      });

      thumbDock.appendChild(thumb);
    });
  }

  /* ---------- STEP 4: Display — renderSlide ---------- */
  function renderSlide(index, direction) {
    const slides = frame.querySelectorAll('.slide');
    const thumbs = thumbDock.querySelectorAll('.thumb');

    slides.forEach((slide, i) => {
      slide.classList.remove('active', 'exit-left', 'exit-right');
      if (i === index) {
        slide.classList.add('active');
      } else if (i === previousIndex) {
        if (direction === 'forward') slide.classList.add('exit-left');
        else if (direction === 'backward') slide.classList.add('exit-right');
      }
    });

    thumbs.forEach((t, i) => t.classList.toggle('active', i === index));
    const activeThumb = thumbDock.querySelector('.thumb.active');
    if (activeThumb) {
      activeThumb.scrollIntoView({ behavior: 'smooth', inline: 'center', block: 'nearest' });
    }

    // caption: re-trigger its fade-in animation on every change
    captionText.textContent = images[index].caption;
    captionText.style.animation = 'none';
    void captionText.offsetWidth; // force reflow
    captionText.style.animation = '';

    counterCurrent.textContent = String(index + 1).padStart(2, '0');

    syncLightboxIfOpen();
  }

  /* ---------- STEP 3: Navigation — next()/prev() with modulo wraparound ---------- */
  function goTo(targetIndex, direction) {
    const total = images.length;
    const normalized = ((targetIndex % total) + total) % total;
    if (normalized === currentIndex) return;

    previousIndex = currentIndex;
    currentIndex = normalized;
    renderSlide(currentIndex, direction);
    resetAutoplay();
  }

  function next() {
    goTo(currentIndex + 1, 'forward');
  }

  function prev() {
    goTo(currentIndex - 1, 'backward');
  }

  /* ---------- STEP 5: Auto-rotation — setInterval / clearInterval ---------- */
  function startAutoplay() {
    clearInterval(autoplayTimer);
    autoplayTimer = setInterval(next, AUTOPLAY_MS);
    ringStart = performance.now();
    cancelAnimationFrame(ringRAF);
    ringRAF = requestAnimationFrame(runRing);
  }

  function stopAutoplay() {
    clearInterval(autoplayTimer);
    autoplayTimer = null;
    cancelAnimationFrame(ringRAF);
    ringRAF = null;
    ringProgress.style.strokeDashoffset = String(RING_CIRCUMFERENCE);
  }

  function resetAutoplay() {
    stopAutoplay();
    if (isPlaying) startAutoplay();
  }

  function runRing(timestamp) {
    const elapsed = performance.now() - ringStart;
    const progress = Math.min(elapsed / AUTOPLAY_MS, 1);
    ringProgress.style.strokeDashoffset = String(RING_CIRCUMFERENCE * (1 - progress));
    if (progress < 1) {
      ringRAF = requestAnimationFrame(runRing);
    }
  }

  function togglePlay() {
    isPlaying = !isPlaying;
    playIcon.textContent = isPlaying ? '❙❙' : '▶';
    playToggle.title = isPlaying ? 'Pause autoplay (Space)' : 'Resume autoplay (Space)';
    if (isPlaying) {
      startAutoplay();
      showToast('Autoplay resumed');
    } else {
      stopAutoplay();
      showToast('Autoplay paused');
    }
  }

  /* ---------- Toast notifications ---------- */
  let toastTimer = null;
  function showToast(message, duration = 2000) {
    toast.textContent = message;
    toast.classList.add('show');
    clearTimeout(toastTimer);
    toastTimer = setTimeout(() => toast.classList.remove('show'), duration);
  }

  /* ---------- Fullscreen lightbox ---------- */
  function openLightbox() {
    lightboxImg.src = images[currentIndex].src;
    lightboxImg.alt = images[currentIndex].caption;
    lightbox.classList.add('open');
    lightbox.setAttribute('aria-hidden', 'false');
    document.body.style.overflow = 'hidden';
  }

  function closeLightbox() {
    lightbox.classList.remove('open');
    lightbox.setAttribute('aria-hidden', 'true');
    document.body.style.overflow = '';
  }

  function syncLightboxIfOpen() {
    if (lightbox.classList.contains('open')) {
      lightboxImg.src = images[currentIndex].src;
      lightboxImg.alt = images[currentIndex].caption;
    }
  }

  /* ---------- Easter egg: confetti burst ---------- */
  const CONFETTI_COLORS = ['#e8b34b', '#f4d597', '#ff6a3d', '#f4efe4', '#8f93a8'];

  function launchConfetti(count = 60) {
    for (let i = 0; i < count; i++) {
      const piece = document.createElement('div');
      piece.className = 'confetti-piece';

      const size = 6 + Math.random() * 6;
      const duration = 2.4 + Math.random() * 1.8;

      piece.style.width = `${size}px`;
      piece.style.height = `${size * 0.4}px`;
      piece.style.left = `${Math.random() * 100}vw`;
      piece.style.background = CONFETTI_COLORS[Math.floor(Math.random() * CONFETTI_COLORS.length)];
      piece.style.animationDuration = `${duration}s`;
      piece.style.animationDelay = `${Math.random() * 0.35}s`;

      confettiLayer.appendChild(piece);
      setTimeout(() => piece.remove(), (duration + 0.4) * 1000);
    }
  }

  /* ---------- Easter egg: Konami code ---------- */
  const KONAMI_SEQUENCE = [
    'ArrowUp', 'ArrowUp', 'ArrowDown', 'ArrowDown',
    'ArrowLeft', 'ArrowRight', 'ArrowLeft', 'ArrowRight',
    'b', 'a',
  ];
  let konamiProgress = 0;

  function trackKonami(key) {
    const expected = KONAMI_SEQUENCE[konamiProgress];
    if (key.toLowerCase() === expected.toLowerCase()) {
      konamiProgress++;
      if (konamiProgress === KONAMI_SEQUENCE.length) {
        konamiProgress = 0;
        activateKonami();
      }
    } else {
      konamiProgress = key.toLowerCase() === KONAMI_SEQUENCE[0].toLowerCase() ? 1 : 0;
    }
  }

  function activateKonami() {
    launchConfetti(140);
    showToast('🎉 secret reel unlocked!', 3000);
    document.documentElement.style.transition = 'filter .6s ease';
    document.documentElement.style.filter = 'hue-rotate(180deg) saturate(1.4)';
    setTimeout(() => {
      document.documentElement.style.filter = '';
    }, 4000);
  }

  /* ---------- Ambient particle field (canvas) ---------- */
  function initParticles() {
    if (!particleCanvas || !particleCanvas.getContext) return;
    const ctx = particleCanvas.getContext('2d');
    const reduceMotion = window.matchMedia('(prefers-reduced-motion: reduce)').matches;

    let width = 0;
    let height = 0;
    let particles = [];
    const COUNT = 45;

    function resize() {
      width = particleCanvas.width = window.innerWidth;
      height = particleCanvas.height = window.innerHeight;
    }

    function seed() {
      particles = Array.from({ length: COUNT }, () => ({
        x: Math.random() * width,
        y: Math.random() * height,
        r: 0.6 + Math.random() * 1.7,
        vx: (Math.random() - 0.5) * 0.15,
        vy: (Math.random() - 0.5) * 0.15,
        baseAlpha: 0.15 + Math.random() * 0.45,
        pulseSpeed: 0.0025 + Math.random() * 0.008,
        pulseOffset: Math.random() * Math.PI * 2,
      }));
    }

    function draw(time) {
      ctx.clearRect(0, 0, width, height);
      particles.forEach((p) => {
        if (!reduceMotion) {
          p.x += p.vx;
          p.y += p.vy;
          if (p.x < 0) p.x = width;
          if (p.x > width) p.x = 0;
          if (p.y < 0) p.y = height;
          if (p.y > height) p.y = 0;
        }
        const pulse = reduceMotion ? 0.6 : (Math.sin(time * p.pulseSpeed + p.pulseOffset) + 1) / 2;
        ctx.beginPath();
        ctx.fillStyle = `rgba(232, 179, 75, ${(p.baseAlpha * (0.4 + pulse * 0.6)).toFixed(3)})`;
        ctx.arc(p.x, p.y, p.r, 0, Math.PI * 2);
        ctx.fill();
      });
      if (!reduceMotion) requestAnimationFrame(draw);
    }

    resize();
    seed();
    window.addEventListener('resize', () => {
      resize();
      seed();
    });
    requestAnimationFrame(draw);
  }

  /* ---------- Live clock ---------- */
  function updateClock() {
    const now = new Date();
    const h = String(now.getHours()).padStart(2, '0');
    const m = String(now.getMinutes()).padStart(2, '0');
    const s = String(now.getSeconds()).padStart(2, '0');
    liveClock.textContent = `${h}:${m}:${s}`;
  }

  /* ---------- Drag / swipe on the stage ---------- */
  let dragging = false;
  let dragStartX = 0;
  const SWIPE_THRESHOLD = 50;

  function isInteractiveTarget(el) {
    return el.closest('button, a, input, textarea');
  }

  function onDragStart(x) {
    dragging = true;
    dragStartX = x;
  }

  function onDragEnd(x) {
    if (!dragging) return;
    dragging = false;
    const delta = x - dragStartX;
    if (Math.abs(delta) > SWIPE_THRESHOLD) {
      delta < 0 ? next() : prev();
    }
  }

  /* ---------- Event wiring ---------- */
  prevBtn.addEventListener('click', prev);
  nextBtn.addEventListener('click', next);
  playToggle.addEventListener('click', togglePlay);

  fullscreenBtn.addEventListener('click', openLightbox);
  lightboxClose.addEventListener('click', closeLightbox);
  lightbox.addEventListener('click', (e) => {
    if (e.target === lightbox) closeLightbox();
  });

  reelLogo.addEventListener('click', () => {
    reelLogo.classList.remove('spin');
    void reelLogo.offsetWidth; // force reflow so the animation can replay
    reelLogo.classList.add('spin');
    launchConfetti(40);
    showToast('🎬 the reel spins on…');
  });

  stage.addEventListener('mousedown', (e) => {
    if (isInteractiveTarget(e.target)) return;
    onDragStart(e.clientX);
  });
  document.addEventListener('mouseup', (e) => onDragEnd(e.clientX));

  stage.addEventListener('touchstart', (e) => {
    if (isInteractiveTarget(e.target)) return;
    onDragStart(e.touches[0].clientX);
  }, { passive: true });
  stage.addEventListener('touchend', (e) => onDragEnd(e.changedTouches[0].clientX));

  document.addEventListener('keydown', (e) => {
    trackKonami(e.key);

    const tag = document.activeElement ? document.activeElement.tagName : '';
    if (tag === 'INPUT' || tag === 'TEXTAREA') return;

    switch (e.key) {
      case 'ArrowRight':
        e.preventDefault();
        next();
        break;
      case 'ArrowLeft':
        e.preventDefault();
        prev();
        break;
      case ' ':
      case 'Spacebar':
        e.preventDefault();
        togglePlay();
        break;
      case 'f':
      case 'F':
        lightbox.classList.contains('open') ? closeLightbox() : openLightbox();
        break;
      case 'Escape':
        if (lightbox.classList.contains('open')) closeLightbox();
        break;
      default:
        break;
    }
  });

  /* ---------- Init ---------- */
  function init() {
    buildSlides();
    buildThumbnails();
    renderSlide(currentIndex, null);
    startAutoplay();
    initParticles();
    updateClock();
    setInterval(updateClock, 1000);
  }

  init();
})();
```

## OUTPUT
<img width="1919" height="1028" alt="Screenshot 2026-09-09 143236" src="https://github.com/user-attachments/assets/4f23855c-2127-42d1-8263-31faea52d941" />
<img width="1918" height="1028" alt="Screenshot 2026-09-09 143509" src="https://github.com/user-attachments/assets/b7b674bd-27f8-4471-a3b0-4100292f7129" />



## RESULT
The program for creating Image Carousel using React is executed successfully.
