# Malaika Wanjihia Personal Website — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a vibrant, single-page personal website deployable to GitHub Pages with Ankara-inspired geometric patterns, anchor navigation, and 7 content sections.

**Architecture:** A single `index.html` links to `style.css` (all visual styles and CSS patterns) and `script.js` (smooth scroll + mobile nav). No build step — GitHub Pages serves files directly. Each content section is a semantic HTML `<section>` with an `id` anchor.

**Tech Stack:** HTML5, CSS3 (custom properties, CSS Grid, Flexbox, inline SVG data-URI patterns), Vanilla JS ES6, Google Fonts CDN, GitHub Pages.

**Spec:** `docs/superpowers/specs/2026-10-14-personal-website-design.md`

---

## Global Constraints

- No frameworks, no npm, no build step — three files only: `index.html`, `style.css`, `script.js`
- Fonts from Google Fonts CDN only: Playfair Display 700, Raleway 700, Inter 400/500
- All colors via CSS custom properties on `:root` — never hardcoded elsewhere in stylesheet
- Text on any colored/patterned background must meet WCAG AA contrast (≥4.5:1 for body, ≥3:1 for large text)
- Images in `assets/images/`: `portrait.jpg`, `equator-kenya.jpg`, `skydiving.jpg`, `hat.jpg`, `cheetah-baby.jpg`
- Breakpoints: mobile `<768px`, tablet `768px–1023px`, desktop `≥1024px`
- Nav labels exactly: About · Research · Experience · Leadership · Beyond the Lab · Contact
- All `<a target="_blank">` must carry `rel="noopener noreferrer"`

---

## Review Focus

1. **Missing image files** — `assets/images/*.jpg` won't exist until the user copies them in. Every `<img>` must have a `background-color` fallback on its container and an `alt` attribute, so a missing file shows a colored placeholder, not a broken icon.
2. **Anchor scroll hidden behind navbar** — the sticky navbar is 68px tall. Without `scroll-padding-top`, clicking a nav link scrolls the section heading under the navbar. The CSS reset must set `scroll-padding-top: var(--nav-height)` on `html`, and Task 11 must verify this visually for every anchor.
3. **Mobile nav not closing on link tap** — tapping a nav anchor link on mobile must close the hamburger dropdown; otherwise the menu stays open over the content. Task 3's `script.js` handles this; Task 11 must verify it in mobile emulation.
4. **External link security** — LinkedIn and Etsy links use `target="_blank"`. Each must have `rel="noopener noreferrer"`. Task 10's verification step checks this explicitly.
5. **Pattern contrast** — white text on the blue diamond-grid hero must meet ≥4.5:1. Teal chip text (`#008f76`) on `rgba(0,201,167,0.15)` background must meet ≥4.5:1. Task 11's Lighthouse audit catches failures; Task 2 notes the specific chip color chosen for this reason.

---

## Task 1: Project Scaffold and Design Tokens

**Files:**
- Create: `index.html`
- Create: `style.css`
- Create: `script.js`
- Create: `assets/images/.gitkeep`

**Interfaces:**
- Consumes: nothing
- Produces:
  - CSS custom properties on `:root`: `--blue`, `--coral`, `--teal`, `--gold`, `--magenta`, `--cream`, `--charcoal`, `--font-display`, `--font-heading`, `--font-body`, `--nav-height`, `--section-pad`, `--container-max`, `--radius`
  - CSS classes: `.container`, `.section-heading`, `.section-label`, `.section-intro`, `.btn`, `.btn-primary`, `.btn-outline`
  - HTML skeleton: `<nav>` with `.navbar` + `.nav-inner`, seven `<section id="...">` stubs, `<footer>`
  - `script.js` with mobile nav toggle and IntersectionObserver for active link

- [ ] **Step 1: Create `index.html`**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Malaika Wanjihia</title>
  <meta name="description" content="PhD Student in Human-Centered Computing at UMBC and Electrical Engineer at JHU Applied Physics Laboratory.">
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Playfair+Display:wght@700&family=Raleway:wght@700&family=Inter:wght@400;500&display=swap" rel="stylesheet">
  <link rel="stylesheet" href="style.css">
</head>
<body>

  <nav class="navbar" id="navbar">
    <div class="container nav-inner">
      <a class="nav-logo" href="#hero">MW</a>
      <button class="hamburger" id="hamburger" aria-label="Toggle navigation" aria-expanded="false">
        <span></span><span></span><span></span>
      </button>
      <ul class="nav-links" id="nav-links">
        <li><a href="#about">About</a></li>
        <li><a href="#research">Research</a></li>
        <li><a href="#experience">Experience</a></li>
        <li><a href="#leadership">Leadership</a></li>
        <li><a href="#personal">Beyond the Lab</a></li>
        <li><a href="#contact">Contact</a></li>
      </ul>
    </div>
  </nav>

  <section id="hero"       class="hero"></section>
  <section id="about"      class="section-about"></section>
  <section id="research"   class="section-research"></section>
  <section id="experience" class="section-experience"></section>
  <section id="leadership" class="section-leadership"></section>
  <section id="personal"   class="section-personal"></section>
  <section id="contact"    class="section-contact"></section>
  <footer class="site-footer"></footer>

  <script src="script.js"></script>
</body>
</html>
```

- [ ] **Step 2: Create `style.css`**

```css
/* === DESIGN TOKENS === */
:root {
  --blue:     #1565C0;
  --coral:    #FF6B35;
  --teal:     #00C9A7;
  --gold:     #FFCA28;
  --magenta:  #E91E8C;
  --cream:    #FFF8F0;
  --charcoal: #1A1A2E;

  --font-display: 'Playfair Display', Georgia, serif;
  --font-heading: 'Raleway', sans-serif;
  --font-body:    'Inter', sans-serif;

  --nav-height:    68px;
  --section-pad:   clamp(3rem, 8vw, 6rem);
  --container-max: 1100px;
  --radius:        12px;
}

/* === RESET === */
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
html {
  scroll-behavior: smooth;
  scroll-padding-top: var(--nav-height);
}
body {
  font-family: var(--font-body);
  background-color: var(--cream);
  color: var(--charcoal);
  line-height: 1.7;
  overflow-x: hidden;
}
img { display: block; max-width: 100%; }
a   { color: inherit; text-decoration: none; }
ul  { list-style: none; }

/* === UTILITIES === */
.container {
  max-width: var(--container-max);
  margin-inline: auto;
  padding-inline: clamp(1rem, 5vw, 2.5rem);
}

.section-heading {
  font-family: var(--font-heading);
  font-size: clamp(1.8rem, 4vw, 2.6rem);
  font-weight: 700;
  line-height: 1.2;
  margin-bottom: 0.5rem;
}

.section-label {
  font-family: var(--font-heading);
  font-size: 0.8rem;
  font-weight: 700;
  letter-spacing: 0.15em;
  text-transform: uppercase;
  margin-bottom: 0.75rem;
}

.section-intro {
  font-size: 1.05rem;
  max-width: 680px;
  margin-bottom: 2.5rem;
  color: #444;
  line-height: 1.8;
}

.btn {
  display: inline-block;
  font-family: var(--font-heading);
  font-weight: 700;
  font-size: 0.95rem;
  padding: 0.75rem 1.75rem;
  border-radius: 50px;
  cursor: pointer;
  transition: transform 0.2s, box-shadow 0.2s;
  border: 2px solid transparent;
}
.btn:hover { transform: translateY(-2px); box-shadow: 0 6px 20px rgba(0,0,0,0.15); }

.btn-primary {
  background: var(--coral);
  color: #fff;
  border-color: var(--coral);
}

.btn-outline {
  background: transparent;
  color: #fff;
  border-color: #fff;
}
.btn-outline:hover { background: rgba(255,255,255,0.15); }
```

- [ ] **Step 3: Create `script.js`**

```js
// Mobile nav toggle
const hamburger = document.getElementById('hamburger');
const navLinks  = document.getElementById('nav-links');

hamburger.addEventListener('click', () => {
  const isOpen = navLinks.classList.toggle('open');
  hamburger.setAttribute('aria-expanded', String(isOpen));
});

// Close nav when any link is tapped
navLinks.querySelectorAll('a').forEach(link => {
  link.addEventListener('click', () => {
    navLinks.classList.remove('open');
    hamburger.setAttribute('aria-expanded', 'false');
  });
});

// Active nav link as user scrolls
const sections = document.querySelectorAll('section[id]');
const navItems = document.querySelectorAll('.nav-links a');

const observer = new IntersectionObserver(entries => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      navItems.forEach(a => a.classList.remove('active'));
      const active = document.querySelector(`.nav-links a[href="#${entry.target.id}"]`);
      if (active) active.classList.add('active');
    }
  });
}, { rootMargin: '-40% 0px -55% 0px' });

sections.forEach(s => observer.observe(s));
```

- [ ] **Step 4: Create `assets/images/.gitkeep`**

Create an empty file at `assets/images/.gitkeep` (touch the file, leave it empty) so git tracks the images directory before the actual photos are added.

- [ ] **Step 5: Verify in browser**

Open `index.html` directly in Chrome or Firefox. Expected: cream background, page title "Malaika Wanjihia" in browser tab, no console errors, Google Fonts loading (check Network tab — three font requests should succeed).

- [ ] **Step 6: Commit**

```bash
git add index.html style.css script.js assets/images/.gitkeep
git commit -m "feat: project scaffold with design tokens, HTML skeleton, and nav script"
```

---

## Task 2: Ankara-Inspired Pattern System

**Files:**
- Modify: `style.css` — append pattern utility classes

**Interfaces:**
- Consumes: CSS tokens from Task 1 (`--blue`, `--coral`, `--gold`, `--teal`, `--magenta`)
- Produces:
  - `.pattern-hero` — blue diamond grid with coral accents; used in hero and contact sections
  - `.pattern-divider` — 24px chevron band in blue + gold; used between sections
  - `.pattern-dots` — subtle cream dot grid; used as research section background
  - `.pattern-triangles` — outline triangles on warm white; used in leadership section
  - `.card-accent-top` — multicolor gradient top border on cards; used on project and research cards

- [ ] **Step 1: Append pattern classes to `style.css`**

```css
/* === ANKARA-INSPIRED PATTERNS === */

/* Diamond grid — hero + contact background */
.pattern-hero {
  background-color: var(--blue);
  background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='60' height='60'%3E%3Cpolygon points='30,4 56,30 30,56 4,30' fill='none' stroke='%23FF6B35' stroke-width='1.5' opacity='0.45'/%3E%3Cpolygon points='30,16 44,30 30,44 16,30' fill='%23FF6B35' fill-opacity='0.12'/%3E%3C/svg%3E");
  background-size: 60px 60px;
}

/* Chevron band — divider between sections */
.pattern-divider {
  height: 28px;
  background-color: var(--blue);
  background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='40' height='28'%3E%3Cpath d='M0 22 L20 6 L40 22' fill='none' stroke='%23FFCA28' stroke-width='2.5'/%3E%3C/svg%3E");
  background-size: 40px 28px;
}

/* Subtle dot grid — research section background */
.pattern-dots {
  background-color: var(--cream);
  background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='28' height='28'%3E%3Ccircle cx='14' cy='14' r='1.5' fill='%231565C0' fill-opacity='0.12'/%3E%3C/svg%3E");
  background-size: 28px 28px;
}

/* Triangle repeat — leadership section background */
.pattern-triangles {
  background-color: #fffbf0;
  background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='48' height='48'%3E%3Cpolygon points='24,4 44,40 4,40' fill='none' stroke='%23FFCA28' stroke-width='1.5' opacity='0.4'/%3E%3C/svg%3E");
  background-size: 48px 48px;
}

/* Multicolor gradient top border — research and project cards */
.card-accent-top {
  overflow: hidden;
}
.card-accent-top::before {
  content: '';
  display: block;
  height: 5px;
  background: linear-gradient(90deg, var(--coral) 0%, var(--gold) 40%, var(--teal) 100%);
}
```

- [ ] **Step 2: Test patterns are visible**

Temporarily add `class="pattern-hero" style="height:200px"` to the hero section and `class="pattern-dots" style="height:200px"` to the research section. Open in browser.

Expected:
- Hero stub: deep blue background with repeating coral diamond outlines
- Research stub: cream background with faint blue dot grid
- Both tile seamlessly — no visible seam at tile edges

- [ ] **Step 3: Remove the temporary inline styles added in step 2**

The `class` attributes can stay (sections will use them in later tasks); remove only the `style="height:200px"` overrides.

- [ ] **Step 4: Commit**

```bash
git add style.css
git commit -m "feat: Ankara-inspired geometric CSS pattern system"
```

---

## Task 3: Sticky Navigation Bar

**Files:**
- Modify: `style.css` — append navbar styles (HTML and JS already written in Task 1)

**Interfaces:**
- Consumes: `.container`, CSS tokens, `#hamburger` / `#nav-links` IDs from Task 1 skeleton
- Produces:
  - `.navbar` — sticky blue bar, 68px tall
  - `.nav-links a.active` — coral underline on the current section's link
  - `.nav-links.open` — dropdown visible on mobile

- [ ] **Step 1: Append navbar styles to `style.css`**

```css
/* === NAVIGATION === */
.navbar {
  position: sticky;
  top: 0;
  z-index: 100;
  background: var(--blue);
  box-shadow: 0 2px 12px rgba(21,101,192,0.25);
  height: var(--nav-height);
}

.nav-inner {
  display: flex;
  align-items: center;
  justify-content: space-between;
  height: 100%;
}

.nav-logo {
  font-family: var(--font-display);
  font-size: 1.5rem;
  font-weight: 700;
  color: #fff;
  letter-spacing: 0.05em;
  transition: color 0.2s;
}
.nav-logo:hover { color: var(--gold); }

.nav-links {
  display: flex;
  gap: 2rem;
  align-items: center;
}

.nav-links a {
  font-family: var(--font-heading);
  font-size: 0.9rem;
  font-weight: 700;
  color: rgba(255,255,255,0.85);
  letter-spacing: 0.05em;
  padding-bottom: 0.2rem;
  border-bottom: 2px solid transparent;
  transition: color 0.2s, border-color 0.2s;
}
.nav-links a:hover,
.nav-links a.active {
  color: #fff;
  border-bottom-color: var(--coral);
}

/* Hamburger button — hidden on desktop */
.hamburger {
  display: none;
  flex-direction: column;
  gap: 5px;
  background: none;
  border: none;
  cursor: pointer;
  padding: 8px;
  min-width: 44px;
  min-height: 44px;
  align-items: center;
  justify-content: center;
}
.hamburger span {
  display: block;
  width: 26px;
  height: 2.5px;
  background: #fff;
  border-radius: 2px;
  transition: transform 0.3s, opacity 0.3s;
}

/* Mobile nav */
@media (max-width: 767px) {
  .hamburger { display: flex; }

  .nav-links {
    display: none;
    flex-direction: column;
    gap: 0;
    position: absolute;
    top: var(--nav-height);
    left: 0;
    right: 0;
    background: var(--blue);
    padding: 0.5rem 0 1rem;
    box-shadow: 0 8px 20px rgba(0,0,0,0.2);
    z-index: 99;
  }
  .nav-links.open { display: flex; }

  .nav-links a {
    padding: 0.85rem 2rem;
    border-bottom: none;
    font-size: 1rem;
    border-bottom: none !important;
    width: 100%;
  }
  .nav-links a:hover,
  .nav-links a.active {
    background: rgba(255,255,255,0.1);
    border-bottom: none !important;
  }
}
```

- [ ] **Step 2: Verify desktop nav**

Open in browser on full width. Expected: sticky blue navbar; "MW" logo on left; six links on right in Raleway font; links get coral underline on hover; page scrolls but nav stays fixed.

- [ ] **Step 3: Verify mobile nav**

Open Chrome DevTools → toggle device toolbar → select iPhone 12 (390px). Expected: hamburger icon appears, links hidden. Tap hamburger → dropdown opens. Tap a link → dropdown closes and page scrolls to section. Tap hamburger again → closes without navigating.

- [ ] **Step 4: Verify active link on scroll** (requires sections to exist — revisit after Task 4)

After Task 4 adds the hero section, scroll past it and confirm the About link activates. Re-verify this step after each section task.

- [ ] **Step 5: Commit**

```bash
git add style.css
git commit -m "feat: sticky navigation bar with mobile hamburger and active-link tracking"
```

---

## Task 4: Hero Section

**Files:**
- Modify: `index.html` — replace empty hero `<section>` with full content
- Modify: `style.css` — append hero styles

**Interfaces:**
- Consumes: `.pattern-hero`, `.btn`, `.btn-primary`, `.btn-outline`, `.container` from prior tasks
- Produces: `#hero` section visible above the fold; portrait placeholder shows coral circle if image missing

- [ ] **Step 1: Replace the empty `<section id="hero">` in `index.html`**

```html
<section id="hero" class="hero pattern-hero">
  <div class="container hero-inner">
    <div class="hero-text">
      <p class="hero-eyebrow">Human-Centered Computing · AI · Engineering</p>
      <h1 class="hero-name">Malaika Wanjihia</h1>
      <p class="hero-title">PhD Student · Human-Centered Computing @ UMBC</p>
      <p class="hero-title">Electrical Engineer · JHU Applied Physics Laboratory</p>
      <p class="hero-tagline">Studying how people trust, use, and maintain agency with AI — so that technology works <em>for</em> everyone.</p>
      <div class="hero-actions">
        <a href="#research" class="btn btn-primary">My Research</a>
        <a href="#contact"  class="btn btn-outline">Say Hello</a>
      </div>
    </div>
    <div class="hero-photo-wrap">
      <img src="assets/images/portrait.jpg" alt="Malaika Wanjihia smiling" class="hero-photo">
    </div>
  </div>
</section>
```

- [ ] **Step 2: Append hero CSS to `style.css`**

```css
/* === HERO === */
.hero {
  padding: clamp(4rem, 10vw, 7rem) 0;
  min-height: calc(100vh - var(--nav-height));
  display: flex;
  align-items: center;
}

.hero-inner {
  display: grid;
  grid-template-columns: 1fr auto;
  gap: 4rem;
  align-items: center;
}

.hero-eyebrow {
  font-family: var(--font-heading);
  font-size: 0.78rem;
  font-weight: 700;
  letter-spacing: 0.18em;
  text-transform: uppercase;
  color: var(--gold);
  margin-bottom: 1rem;
}

.hero-name {
  font-family: var(--font-display);
  font-size: clamp(2.8rem, 7vw, 5rem);
  font-weight: 700;
  color: #fff;
  line-height: 1.1;
  margin-bottom: 0.75rem;
}

.hero-title {
  font-family: var(--font-heading);
  font-size: clamp(0.9rem, 1.8vw, 1.05rem);
  color: rgba(255,255,255,0.9);
  line-height: 1.5;
  margin-bottom: 0.2rem;
}

.hero-tagline {
  font-size: clamp(0.95rem, 1.8vw, 1.1rem);
  color: rgba(255,255,255,0.8);
  max-width: 520px;
  margin: 1.5rem 0 2rem;
  line-height: 1.85;
}
.hero-tagline em {
  color: var(--gold);
  font-style: normal;
  font-weight: 500;
}

.hero-actions {
  display: flex;
  gap: 1rem;
  flex-wrap: wrap;
}

.hero-photo-wrap { flex-shrink: 0; }

.hero-photo {
  width: clamp(200px, 26vw, 320px);
  aspect-ratio: 1;
  border-radius: 50%;
  object-fit: cover;
  border: 5px solid var(--coral);
  box-shadow: 0 0 0 10px rgba(255,107,53,0.18), 0 20px 60px rgba(0,0,0,0.3);
  background-color: rgba(255,107,53,0.2);
}

@media (max-width: 767px) {
  .hero-inner {
    grid-template-columns: 1fr;
    text-align: center;
    gap: 2.5rem;
  }
  .hero-photo-wrap { order: -1; margin-inline: auto; }
  .hero-photo { width: clamp(160px, 55vw, 240px); }
  .hero-actions { justify-content: center; }
  .hero-tagline { margin-inline: auto; }
}
```

- [ ] **Step 3: Verify in browser**

Expected: viewport-tall hero section with blue diamond-grid pattern; name in large white Playfair Display; gold eyebrow text; coral circular portrait placeholder (if image missing, shows a coral-tinted circle); two buttons. On mobile: photo stacks above text, all centered.

- [ ] **Step 4: Commit**

```bash
git add index.html style.css
git commit -m "feat: hero section with Ankara pattern, portrait, and CTA buttons"
```

---

## Task 5: About Section

**Files:**
- Modify: `index.html` — replace empty about section
- Modify: `style.css` — append about section styles

**Interfaces:**
- Consumes: `.container`, `.section-heading`, `.section-label`, CSS tokens from Task 1
- Produces: `#about` section; `.badge` and `.badge-*` color variants available for reuse

- [ ] **Step 1: Replace `<section id="about">` in `index.html`**

```html
<section id="about" class="section-about">
  <div class="container about-inner">
    <div class="about-photo-wrap">
      <img src="assets/images/equator-kenya.jpg" alt="Malaika standing at the equator in Kenya" class="about-photo">
      <div class="about-photo-caption">On the equator — Kenya</div>
    </div>
    <div class="about-content">
      <p class="section-label" style="color: var(--blue);">About Me</p>
      <h2 class="section-heading">Engineer. Researcher. Kenyan.</h2>
      <p>I'm a PhD student in Human-Centered Computing at UMBC and an Electrical Engineer at the Johns Hopkins University Applied Physics Laboratory. Originally from Kenya, I'm driven by one question: how can AI systems truly serve everyone — especially those with the most to gain and the most to lose?</p>
      <p>My research lives at the intersection of technology, trust, and human dignity — examining how vulnerable populations calibrate trust in AI, and how data workers who power humanoid robotic systems experience privacy and physical impact along the way.</p>
      <p>Outside the lab, I run a hat business, jump out of planes, chase new food experiences around the world, and mentor the next generation of engineers and researchers.</p>
      <div class="about-badges">
        <span class="badge badge-blue">PhD · UMBC</span>
        <span class="badge badge-coral">MS · Johns Hopkins</span>
        <span class="badge badge-teal">BS · Morgan State</span>
        <span class="badge badge-gold">JHU APL Engineer</span>
        <span class="badge badge-outline">Top Secret Clearance</span>
      </div>
    </div>
  </div>
</section>
<div class="pattern-divider"></div>
```

- [ ] **Step 2: Append about CSS to `style.css`**

```css
/* === ABOUT === */
.section-about {
  padding: var(--section-pad) 0;
  background-color: var(--cream);
}

.about-inner {
  display: grid;
  grid-template-columns: 320px 1fr;
  gap: 4rem;
  align-items: start;
}

.about-photo-wrap { position: relative; }

.about-photo {
  width: 100%;
  border-radius: var(--radius);
  object-fit: cover;
  aspect-ratio: 4/5;
  background-color: rgba(21,101,192,0.1);
  box-shadow: 8px 8px 0 var(--coral);
}

.about-photo-caption {
  position: absolute;
  bottom: -1.1rem;
  left: 50%;
  transform: translateX(-50%);
  background: var(--blue);
  color: #fff;
  font-family: var(--font-heading);
  font-size: 0.72rem;
  font-weight: 700;
  letter-spacing: 0.1em;
  padding: 0.35rem 1rem;
  border-radius: 50px;
  white-space: nowrap;
}

.about-content p {
  margin-bottom: 1.1rem;
  font-size: 1.02rem;
  line-height: 1.85;
}

.about-badges {
  display: flex;
  flex-wrap: wrap;
  gap: 0.6rem;
  margin-top: 1.75rem;
}

.badge {
  font-family: var(--font-heading);
  font-size: 0.78rem;
  font-weight: 700;
  letter-spacing: 0.06em;
  padding: 0.4rem 1rem;
  border-radius: 50px;
}
.badge-blue    { background: var(--blue);    color: #fff; }
.badge-coral   { background: var(--coral);   color: #fff; }
.badge-teal    { background: var(--teal);    color: #fff; }
.badge-gold    { background: var(--gold);    color: var(--charcoal); }
.badge-outline { background: transparent; border: 2px solid var(--charcoal); color: var(--charcoal); }

@media (max-width: 767px) {
  .about-inner { grid-template-columns: 1fr; gap: 3rem; }
  .about-photo { aspect-ratio: 4/3; }
}
```

- [ ] **Step 3: Verify in browser**

Expected: equator photo with coral box-shadow offset and blue pill caption below; bio text to the right; five colored badges at bottom including "Top Secret Clearance" as an outline badge. Chevron divider below section. On mobile: single column, photo on top.

- [ ] **Step 4: Commit**

```bash
git add index.html style.css
git commit -m "feat: about section with equator photo, bio, and credential badges"
```

---

## Task 6: Research Section

**Files:**
- Modify: `index.html` — replace empty research section
- Modify: `style.css` — append research section styles

**Interfaces:**
- Consumes: `.container`, `.section-heading`, `.section-label`, `.section-intro`, `.card-accent-top`, CSS tokens
- Produces: `#research` with two teal-accented cards and a publications placeholder

- [ ] **Step 1: Replace `<section id="research">` in `index.html`**

```html
<section id="research" class="section-research pattern-dots">
  <div class="container">
    <p class="section-label" style="color: var(--teal);">Research</p>
    <h2 class="section-heading">Understanding People in the Age of AI</h2>
    <p class="section-intro">My work examines the human side of artificial intelligence — how people develop trust, maintain agency, and experience the costs of the systems that shape their lives.</p>

    <div class="research-grid">
      <div class="research-card card-accent-top">
        <div class="research-card-body">
          <span class="research-tag">PhD Research · UMBC</span>
          <h3>Trust Calibration in LLMs Among Vulnerable Populations</h3>
          <p>How do people with limited AI literacy — older adults, those with lower digital access, non-English speakers — decide how much to trust large language models? I investigate how these groups calibrate trust, how they use AI to their benefit, and what factors preserve or erode their agency and autonomy in human-AI interactions.</p>
        </div>
      </div>
      <div class="research-card card-accent-top">
        <div class="research-card-body">
          <span class="research-tag">Current Project · UMBC</span>
          <h3>Privacy and Physical Impacts of Humanoid Robotic Data Work</h3>
          <p>The development of humanoid robots depends on human data workers who annotate, label, and generate training data — often in conditions that carry real privacy and physical costs. This project examines those experiences and what they reveal about the hidden human labor behind AI systems.</p>
        </div>
      </div>
    </div>

    <div class="publications-placeholder">
      <h3>Publications &amp; Presentations</h3>
      <p>Coming soon — watch this space as research progresses.</p>
    </div>
  </div>
</section>
<div class="pattern-divider"></div>
```

- [ ] **Step 2: Append research CSS to `style.css`**

```css
/* === RESEARCH === */
.section-research { padding: var(--section-pad) 0; }

.research-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 1.75rem;
  margin-bottom: 2.5rem;
}

.research-card {
  background: #fff;
  border-radius: var(--radius);
  box-shadow: 0 4px 20px rgba(0,0,0,0.07);
  transition: transform 0.25s, box-shadow 0.25s;
}
.research-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 10px 30px rgba(0,0,0,0.12);
}

.research-card-body { padding: 1.75rem; }

.research-tag {
  display: inline-block;
  background: var(--teal);
  color: #fff;
  font-family: var(--font-heading);
  font-size: 0.72rem;
  font-weight: 700;
  letter-spacing: 0.1em;
  text-transform: uppercase;
  padding: 0.3rem 0.85rem;
  border-radius: 50px;
  margin-bottom: 1rem;
}

.research-card h3 {
  font-family: var(--font-heading);
  font-size: 1.05rem;
  font-weight: 700;
  line-height: 1.4;
  margin-bottom: 0.75rem;
  color: var(--charcoal);
}

.research-card p {
  font-size: 0.95rem;
  line-height: 1.78;
  color: #555;
}

.publications-placeholder {
  background: rgba(0,201,167,0.08);
  border: 2px dashed var(--teal);
  border-radius: var(--radius);
  padding: 1.75rem;
  text-align: center;
}
.publications-placeholder h3 {
  font-family: var(--font-heading);
  font-size: 1rem;
  font-weight: 700;
  color: var(--teal);
  margin-bottom: 0.4rem;
}
.publications-placeholder p { color: #666; font-size: 0.92rem; }

@media (max-width: 767px) {
  .research-grid { grid-template-columns: 1fr; }
}
```

- [ ] **Step 3: Verify in browser**

Expected: dot-grid background; two white cards with multicolor top accent strip and teal pill tags; cards lift on hover; dashed teal publications box below. Chevron divider below.

- [ ] **Step 4: Commit**

```bash
git add index.html style.css
git commit -m "feat: research section with two research cards and publications placeholder"
```

---

## Task 7: Experience Section

**Files:**
- Modify: `index.html` — replace empty experience section
- Modify: `style.css` — append experience styles

**Interfaces:**
- Consumes: `.container`, `.section-heading`, `.section-label`, `.card-accent-top`, CSS tokens
- Produces: `#experience` with employer blocks, project cards, skill chips, education timeline

- [ ] **Step 1: Replace `<section id="experience">` in `index.html`**

```html
<section id="experience" class="section-experience">
  <div class="container">
    <p class="section-label" style="color: var(--coral);">Experience</p>
    <h2 class="section-heading">Where I've Built Things</h2>

    <div class="employer-block">
      <div class="employer-header">
        <h3 class="employer-name">Johns Hopkins Applied Physics Laboratory</h3>
        <p class="employer-meta">Electrical Engineer · January 2021 – Present · Laurel, MD</p>
      </div>
      <div class="project-cards">
        <div class="project-card card-accent-top">
          <h4>HAYSTAC — Metrics &amp; Evaluation</h4>
          <p>Led anomaly-detection evaluation metrics and automated Python scoring pipelines, enabling repeatable performer assessment and novel pattern-of-life analysis using AWS S3.</p>
        </div>
        <div class="project-card card-accent-top">
          <h4>Athena.AI — Agentic AI &amp; LLM Observability</h4>
          <p>Prototyped Langfuse and OpenTelemetry integration for an LLM Agent Orchestrator; developed Figma wireframes and briefed sponsors on observability architecture and AI transparency.</p>
        </div>
        <div class="project-card card-accent-top">
          <h4>Proof Positive — Intelligence Analysis</h4>
          <p>Led full-stack development of an AI conflict-detection tool using RAG and knowledge graphs; applied agentic workflows across UI, back-end services, and Docker deployment.</p>
        </div>
        <div class="project-card card-accent-top">
          <h4>DoD Health Analytics</h4>
          <p>Applied unsupervised ML and topic modeling to clinical data for novel health threat detection; facilitated sponsor briefings and built human-centered traceability frameworks.</p>
        </div>
        <div class="project-card card-accent-top">
          <h4>Public Health Disaster Response Tool</h4>
          <p>Built data pipelines for civilian medical datasets; explored LLM integration for data discovery to support an operational public-health dashboard.</p>
        </div>
        <div class="project-card card-accent-top">
          <h4>Unified Network Operations (UNO)</h4>
          <p>Developed an MBSE framework in Cameo capturing hundreds of requirements; authored UI requirements and facilitated soldier touch-point engagements with sponsors.</p>
        </div>
        <div class="project-card card-accent-top">
          <h4>Cross-Medium Communications R&amp;D</h4>
          <p>Led a team of five on FMCW radar R&amp;D; managed budgets, coordinated hardware development, and prepared research proposals.</p>
        </div>
        <div class="project-card card-accent-top">
          <h4>Miniature Devices with Advanced RF Capabilities</h4>
          <p>Designed PCBs in Altium; developed automated acceptance tests and led test and debug efforts for multiple RF and embedded systems.</p>
        </div>
      </div>
    </div>

    <div class="employer-block">
      <div class="employer-header">
        <h3 class="employer-name">Lockheed Martin</h3>
        <p class="employer-meta">Engineering Roles · 2018 – 2021</p>
      </div>
      <div class="project-cards">
        <div class="project-card card-accent-top">
          <h4>Hypersonics &amp; F-35 Joint Strike Fighter Programs</h4>
          <p>FPGA design in VHDL/SystemVerilog, interface verification (SPI/UART/IMU), automated testing, functional coverage analysis, and simulation across hypersonic missile and F-35 programs.</p>
        </div>
      </div>
    </div>

    <div class="skills-block">
      <h3 class="skills-heading">Technical Skills</h3>
      <div class="skills-group">
        <span class="skills-label">AI / ML</span>
        <div class="skill-chips">
          <span class="chip chip-teal">LLM Integration</span>
          <span class="chip chip-teal">RAG</span>
          <span class="chip chip-teal">Knowledge Graphs</span>
          <span class="chip chip-teal">Agentic AI</span>
          <span class="chip chip-teal">Topic Modeling</span>
          <span class="chip chip-teal">Anomaly Detection</span>
          <span class="chip chip-teal">Langfuse</span>
          <span class="chip chip-teal">OpenTelemetry</span>
        </div>
      </div>
      <div class="skills-group">
        <span class="skills-label">Engineering &amp; Data</span>
        <div class="skill-chips">
          <span class="chip chip-coral">Python</span>
          <span class="chip chip-coral">Data Pipelines</span>
          <span class="chip chip-coral">AWS S3</span>
          <span class="chip chip-coral">MBSE (Cameo)</span>
          <span class="chip chip-coral">PCB Design (Altium)</span>
          <span class="chip chip-coral">FPGA (VHDL)</span>
          <span class="chip chip-coral">RF Test &amp; Measurement</span>
        </div>
      </div>
      <div class="skills-group">
        <span class="skills-label">Design &amp; Research</span>
        <div class="skill-chips">
          <span class="chip chip-blue">Figma</span>
          <span class="chip chip-blue">Human-Centered Design</span>
          <span class="chip chip-blue">User Research</span>
          <span class="chip chip-blue">Requirements Engineering</span>
          <span class="chip chip-blue">Prompt Engineering</span>
        </div>
      </div>
    </div>

    <div class="education-block">
      <h3 class="skills-heading">Education</h3>
      <div class="education-timeline">
        <div class="edu-item">
          <div class="edu-dot" style="background: var(--blue);"></div>
          <div>
            <h4>Doctor of Philosophy — Human-Centered Computing</h4>
            <p>University of Maryland, Baltimore County (UMBC) · In Progress</p>
          </div>
        </div>
        <div class="edu-item">
          <div class="edu-dot" style="background: var(--coral);"></div>
          <div>
            <h4>Master of Science — Electrical &amp; Computer Engineering · GPA 3.8</h4>
            <p>Johns Hopkins University · May 2023</p>
          </div>
        </div>
        <div class="edu-item">
          <div class="edu-dot" style="background: var(--teal);"></div>
          <div>
            <h4>Bachelor of Science — Electrical Engineering · GPA 3.9</h4>
            <p>Morgan State University · December 2020</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</section>
<div class="pattern-divider"></div>
```

- [ ] **Step 2: Append experience CSS to `style.css`**

```css
/* === EXPERIENCE === */
.section-experience {
  padding: var(--section-pad) 0;
  background: var(--cream);
}

.employer-block { margin-bottom: 3rem; }

.employer-header {
  margin-bottom: 1.25rem;
  padding-bottom: 0.9rem;
  border-bottom: 3px solid var(--coral);
}

.employer-name {
  font-family: var(--font-heading);
  font-size: 1.25rem;
  font-weight: 700;
  color: var(--charcoal);
}

.employer-meta {
  font-size: 0.88rem;
  color: #666;
  margin-top: 0.2rem;
}

.project-cards {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(270px, 1fr));
  gap: 1.25rem;
}

.project-card {
  background: #fff;
  border-radius: var(--radius);
  box-shadow: 0 2px 12px rgba(0,0,0,0.06);
}

.project-card h4 {
  font-family: var(--font-heading);
  font-size: 0.92rem;
  font-weight: 700;
  color: var(--charcoal);
  margin-bottom: 0.55rem;
  padding: 1.25rem 1.25rem 0;
}

.project-card p {
  font-size: 0.87rem;
  line-height: 1.7;
  color: #555;
  padding: 0 1.25rem 1.25rem;
}

/* Skills */
.skills-block, .education-block { margin-bottom: 2.5rem; }

.skills-heading {
  font-family: var(--font-heading);
  font-size: 1.1rem;
  font-weight: 700;
  margin-bottom: 1.25rem;
  color: var(--charcoal);
}

.skills-group {
  display: flex;
  align-items: flex-start;
  gap: 1rem;
  margin-bottom: 0.85rem;
  flex-wrap: wrap;
}

.skills-label {
  font-family: var(--font-heading);
  font-size: 0.74rem;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 0.08em;
  color: #888;
  padding-top: 0.35rem;
  min-width: 140px;
}

.skill-chips { display: flex; flex-wrap: wrap; gap: 0.5rem; }

.chip {
  font-family: var(--font-heading);
  font-size: 0.77rem;
  font-weight: 700;
  padding: 0.3rem 0.85rem;
  border-radius: 50px;
}
/* Chip colors chosen for ≥4.5:1 contrast on their backgrounds */
.chip-teal  { background: rgba(0,201,167,0.14);  color: #007a65; }
.chip-coral { background: rgba(255,107,53,0.12);  color: #b5400c; }
.chip-blue  { background: rgba(21,101,192,0.12);  color: #0d4899; }

/* Education */
.education-timeline {
  display: flex;
  flex-direction: column;
  gap: 1.25rem;
  padding-left: 0.5rem;
}

.edu-item {
  display: flex;
  gap: 1.25rem;
  align-items: flex-start;
}

.edu-dot {
  width: 14px;
  height: 14px;
  border-radius: 50%;
  flex-shrink: 0;
  margin-top: 0.35rem;
}

.edu-item h4 {
  font-family: var(--font-heading);
  font-size: 0.93rem;
  font-weight: 700;
  color: var(--charcoal);
  margin-bottom: 0.2rem;
}

.edu-item p { font-size: 0.87rem; color: #666; }
```

- [ ] **Step 3: Verify in browser**

Expected: coral-underlined employer headers; auto-fill project card grid (3 columns on wide, 2 on medium, 1 on mobile); multicolor top accent strip on each card; colored skill chips grouped by domain; three-dot education timeline with colored dots.

- [ ] **Step 4: Commit**

```bash
git add index.html style.css
git commit -m "feat: experience section with project cards, skill chips, and education timeline"
```

---

## Task 8: Leadership & Service Section

**Files:**
- Modify: `index.html` — replace empty leadership section
- Modify: `style.css` — append leadership styles

**Interfaces:**
- Consumes: `.container`, `.section-heading`, `.section-label`, `.section-intro`, `.pattern-triangles`, CSS tokens
- Produces: `#leadership` with four gold-top-bordered cards on triangle-pattern background

- [ ] **Step 1: Replace `<section id="leadership">` in `index.html`**

```html
<section id="leadership" class="section-leadership pattern-triangles">
  <div class="container">
    <p class="section-label" style="color: #b8920a;">Leadership &amp; Service</p>
    <h2 class="section-heading">Giving Back, Building Up</h2>
    <p class="section-intro">Community and mentorship aren't extracurriculars to me — they're core to how I show up in every space I occupy.</p>

    <div class="leadership-grid">
      <div class="leadership-card">
        <div class="leadership-icon">🎓</div>
        <h3>BRIDGE Mentoring Program Chair</h3>
        <p>Developed and led a mentoring program with 25 mentors and 25 mentees per cycle; managed a team of 10 staff; 40% of student participants received summer internship offers. Co-coordinated with Morgan State University leadership.</p>
      </div>
      <div class="leadership-card">
        <div class="leadership-icon">✊</div>
        <h3>Black Faculty &amp; Staff Association Caucus Chair</h3>
        <p>Led the BFSA APL Caucus; partnered with APL and JHU leadership; planned and facilitated events with 80+ attendees; coordinated speakers, managed budgets, and handled cross-lab coordination.</p>
      </div>
      <div class="leadership-card">
        <div class="leadership-icon">🔬</div>
        <h3>Instrument Coordinator</h3>
        <p>Serviced three groups with 180 staff, ensuring calibration and availability of 50+ essential electronics instruments for RF device testing and development across teams.</p>
      </div>
      <div class="leadership-card">
        <div class="leadership-icon">🌱</div>
        <h3>Mentor &amp; Community Builder</h3>
        <p>Mentored students and interns at every stage of their journeys; supported community-building initiatives through programs focused on belonging, professional development, and representation in STEM.</p>
      </div>
    </div>
  </div>
</section>
<div class="pattern-divider"></div>
```

- [ ] **Step 2: Append leadership CSS to `style.css`**

```css
/* === LEADERSHIP === */
.section-leadership { padding: var(--section-pad) 0; }

.leadership-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
  gap: 1.5rem;
  margin-top: 2rem;
}

.leadership-card {
  background: #fff;
  border-radius: var(--radius);
  padding: 2rem 1.75rem;
  box-shadow: 0 4px 20px rgba(0,0,0,0.07);
  border-top: 5px solid var(--gold);
  transition: transform 0.25s, box-shadow 0.25s;
}
.leadership-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 10px 30px rgba(0,0,0,0.12);
}

.leadership-icon {
  font-size: 2.25rem;
  margin-bottom: 0.9rem;
  line-height: 1;
}

.leadership-card h3 {
  font-family: var(--font-heading);
  font-size: 0.98rem;
  font-weight: 700;
  color: var(--charcoal);
  margin-bottom: 0.65rem;
  line-height: 1.4;
}

.leadership-card p {
  font-size: 0.9rem;
  line-height: 1.75;
  color: #555;
}
```

- [ ] **Step 3: Verify in browser**

Expected: triangle-pattern warm-white background; four white cards with gold top border; emoji icons; cards lift on hover. Note: the `.section-label` uses `#b8920a` (darker gold) instead of `--gold` (#FFCA28) to ensure readable contrast on the light background.

- [ ] **Step 4: Commit**

```bash
git add index.html style.css
git commit -m "feat: leadership and service section with four gold-accented cards"
```

---

## Task 9: Beyond the Lab Section

**Files:**
- Modify: `index.html` — replace empty personal section
- Modify: `style.css` — append personal section styles

**Interfaces:**
- Consumes: `.container`, `.section-heading`, `.section-label`, `.section-intro`, `.btn`, CSS tokens
- Produces: `#personal` with 2×2 photo grid, hat business feature card, four interest blurbs; `.btn-magenta` available for reuse

- [ ] **Step 1: Replace `<section id="personal">` in `index.html`**

```html
<section id="personal" class="section-personal">
  <div class="container">
    <p class="section-label" style="color: var(--magenta);">Beyond the Lab</p>
    <h2 class="section-heading">Life Outside Research</h2>
    <p class="section-intro">A researcher is only as interesting as the life they bring to the work. Here's mine.</p>

    <div class="personal-grid">
      <div class="personal-photo-card">
        <img src="assets/images/skydiving.jpg" alt="Malaika skydiving" loading="lazy">
        <div class="photo-caption">Skydiving — because why not?</div>
      </div>
      <div class="personal-photo-card">
        <img src="assets/images/hat.jpg" alt="Malaika wearing one of her hats" loading="lazy">
        <div class="photo-caption">Wearing one of my own creations</div>
      </div>
      <div class="personal-photo-card">
        <img src="assets/images/cheetah-baby.jpg" alt="Baby Malaika with a cheetah in Kenya" loading="lazy">
        <div class="photo-caption">Baby me with a cheetah in Kenya 🐆</div>
      </div>
      <div class="personal-photo-card">
        <img src="assets/images/equator-kenya.jpg" alt="Malaika on the equator in Kenya" loading="lazy">
        <div class="photo-caption">Straddling hemispheres — equator, Kenya</div>
      </div>
    </div>

    <div class="hat-feature-card">
      <div class="hat-feature-text">
        <p class="section-label" style="color: var(--magenta);">Side Hustle</p>
        <h3>Malaika's Hats</h3>
        <p>I design and sell handcrafted hats through my Etsy shop. Whether it's a bold statement piece or an everyday wear, each hat is made with care and a whole lot of personality.</p>
        <a href="https://www.etsy.com/shop/MalaikasHats" target="_blank" rel="noopener noreferrer" class="btn btn-magenta">Shop Malaika's Hats →</a>
      </div>
      <div class="hat-feature-img">
        <img src="assets/images/hat.jpg" alt="One of Malaika's handcrafted hats" loading="lazy">
      </div>
    </div>

    <div class="interests-grid">
      <div class="interest-item">
        <span class="interest-icon">✈️</span>
        <h4>World Traveler</h4>
        <p>Kenya is home, but the whole world is on my itinerary.</p>
      </div>
      <div class="interest-item">
        <span class="interest-icon">🍜</span>
        <h4>Food Explorer</h4>
        <p>New city, new restaurant, always. Food is how I explore culture.</p>
      </div>
      <div class="interest-item">
        <span class="interest-icon">🪂</span>
        <h4>Adrenaline Junkie</h4>
        <p>Skydiving, thrill-seeking — if it gets the heart going, I'm in.</p>
      </div>
      <div class="interest-item">
        <span class="interest-icon">🤝</span>
        <h4>Community First</h4>
        <p>Volunteering and service aren't things I do — they're part of who I am.</p>
      </div>
    </div>
  </div>
</section>
<div class="pattern-divider"></div>
```

- [ ] **Step 2: Append personal section CSS to `style.css`**

```css
/* === BEYOND THE LAB === */
.section-personal {
  padding: var(--section-pad) 0;
  background: var(--cream);
}

.personal-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 1.25rem;
  margin-bottom: 3rem;
}

.personal-photo-card {
  position: relative;
  border-radius: var(--radius);
  overflow: hidden;
  aspect-ratio: 4/3;
  background-color: rgba(233,30,140,0.1);
}

.personal-photo-card img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: transform 0.4s ease;
}
.personal-photo-card:hover img { transform: scale(1.04); }

.photo-caption {
  position: absolute;
  bottom: 0; left: 0; right: 0;
  background: linear-gradient(transparent, rgba(26,26,46,0.82));
  color: #fff;
  font-family: var(--font-heading);
  font-size: 0.85rem;
  font-weight: 700;
  padding: 2rem 1.1rem 0.9rem;
}

/* Hat feature card */
.hat-feature-card {
  background: linear-gradient(135deg, rgba(233,30,140,0.07) 0%, rgba(255,107,53,0.07) 100%);
  border: 2px solid var(--magenta);
  border-radius: var(--radius);
  display: grid;
  grid-template-columns: 1fr 260px;
  gap: 2rem;
  align-items: center;
  padding: 2.5rem;
  margin-bottom: 3rem;
}

.hat-feature-text h3 {
  font-family: var(--font-display);
  font-size: 1.8rem;
  font-weight: 700;
  color: var(--charcoal);
  margin-bottom: 0.75rem;
}

.hat-feature-text p {
  font-size: 1rem;
  line-height: 1.75;
  color: #555;
  margin-bottom: 1.5rem;
}

.btn-magenta {
  background: var(--magenta);
  color: #fff;
  border-color: var(--magenta);
}
.btn-magenta:hover { background: #c41876; border-color: #c41876; }

.hat-feature-img img {
  width: 100%;
  aspect-ratio: 1;
  object-fit: cover;
  border-radius: var(--radius);
  background-color: rgba(233,30,140,0.1);
}

/* Interests grid */
.interests-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 1.25rem;
}

.interest-item {
  background: #fff;
  border-radius: var(--radius);
  padding: 1.5rem 1.25rem;
  text-align: center;
  box-shadow: 0 2px 12px rgba(0,0,0,0.06);
}

.interest-icon {
  font-size: 2rem;
  display: block;
  margin-bottom: 0.75rem;
}

.interest-item h4 {
  font-family: var(--font-heading);
  font-size: 0.93rem;
  font-weight: 700;
  color: var(--charcoal);
  margin-bottom: 0.4rem;
}

.interest-item p { font-size: 0.85rem; color: #666; line-height: 1.6; }

@media (max-width: 767px) {
  .personal-grid { grid-template-columns: 1fr; }
  .hat-feature-card { grid-template-columns: 1fr; }
  .hat-feature-img { display: none; }
  .interests-grid { grid-template-columns: repeat(2, 1fr); }
}
```

- [ ] **Step 3: Verify in browser**

Expected: 2×2 photo grid with magenta-tinted placeholders (if images missing), gradient caption overlays, zoom-on-hover; magenta-bordered hat feature card with Etsy button; four interest blurbs in a row below. On mobile: photos stack, hat image hidden, interests 2×2.

- [ ] **Step 4: Commit**

```bash
git add index.html style.css
git commit -m "feat: Beyond the Lab section with photo grid, hat business card, and interest blurbs"
```

---

## Task 10: Contact Section and Footer

**Files:**
- Modify: `index.html` — replace empty contact section and footer
- Modify: `style.css` — append contact and footer styles

**Interfaces:**
- Consumes: `.container`, `.section-heading`, `.section-label`, `.pattern-hero`, CSS tokens
- Produces: `#contact` with three pill contact links; `.site-footer` with name + copyright

- [ ] **Step 1: Replace `<section id="contact">` and `<footer>` in `index.html`**

```html
<section id="contact" class="section-contact pattern-hero">
  <div class="container contact-inner">
    <p class="section-label" style="color: var(--gold);">Contact</p>
    <h2 class="section-heading" style="color: #fff;">Let's Connect</h2>
    <p class="contact-sub">Whether you want to talk research, collaboration, mentorship, or hats — my inbox is open.</p>
    <div class="contact-links">
      <a href="mailto:wanjihiam16@gmail.com" class="contact-link">
        <span class="contact-icon">✉️</span>
        <span>wanjihiam16@gmail.com</span>
      </a>
      <a href="https://www.linkedin.com/in/malaika-wanjihia-302a06141/" target="_blank" rel="noopener noreferrer" class="contact-link">
        <span class="contact-icon">💼</span>
        <span>LinkedIn</span>
      </a>
      <a href="https://www.etsy.com/shop/MalaikasHats" target="_blank" rel="noopener noreferrer" class="contact-link">
        <span class="contact-icon">🎩</span>
        <span>Malaika's Hats on Etsy</span>
      </a>
    </div>
  </div>
</section>

<footer class="site-footer pattern-hero">
  <div class="container footer-inner">
    <span class="footer-name">Malaika Wanjihia</span>
    <span class="footer-copy">Built with ♥ · © 2026</span>
  </div>
</footer>
```

- [ ] **Step 2: Append contact and footer CSS to `style.css`**

```css
/* === CONTACT === */
.section-contact { padding: var(--section-pad) 0; }

.contact-inner {
  text-align: center;
  max-width: 680px;
  margin-inline: auto;
}

.contact-sub {
  color: rgba(255,255,255,0.85);
  font-size: 1.05rem;
  line-height: 1.75;
  margin: 1rem 0 2.5rem;
}

.contact-links {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  align-items: center;
}

.contact-link {
  display: flex;
  align-items: center;
  gap: 0.85rem;
  background: rgba(255,255,255,0.12);
  border: 1.5px solid rgba(255,255,255,0.3);
  border-radius: 50px;
  padding: 0.85rem 2rem;
  color: #fff;
  font-family: var(--font-heading);
  font-weight: 700;
  font-size: 1rem;
  transition: background 0.2s, transform 0.2s;
  min-width: 300px;
  justify-content: center;
}
.contact-link:hover {
  background: rgba(255,255,255,0.22);
  transform: translateY(-2px);
}

.contact-icon { font-size: 1.2rem; }

/* === FOOTER === */
.site-footer {
  padding: 1.5rem 0;
  border-top: 1px solid rgba(255,255,255,0.15);
}

.footer-inner {
  display: flex;
  align-items: center;
  justify-content: space-between;
  flex-wrap: wrap;
  gap: 0.5rem;
}

.footer-name {
  font-family: var(--font-display);
  font-size: 1rem;
  color: rgba(255,255,255,0.9);
}

.footer-copy {
  font-size: 0.82rem;
  color: rgba(255,255,255,0.5);
}

@media (max-width: 767px) {
  .contact-link { min-width: unset; width: 100%; }
  .footer-inner { justify-content: center; text-align: center; }
}
```

- [ ] **Step 3: Verify in browser**

Expected: contact section reuses blue diamond-grid pattern; three pill-shaped links centered (email, LinkedIn, Etsy); footer with name left and copyright right on same blue pattern.

- [ ] **Step 4: Verify external link security**

Inspect the LinkedIn and Etsy `<a>` tags. Both must have `rel="noopener noreferrer"` and `target="_blank"`. Confirm this in the browser's Elements panel.

- [ ] **Step 5: Commit**

```bash
git add index.html style.css
git commit -m "feat: contact section and footer with email, LinkedIn, and Etsy links"
```

---

## Task 11: Responsive Polish and Accessibility Audit

**Files:**
- Modify: `style.css` — add any missing tablet/mobile rules found during audit
- Modify: `index.html` — fix any markup issues found

**Interfaces:**
- Consumes: all sections from Tasks 4–10
- Produces: site passes Lighthouse Accessibility ≥ 90; no horizontal scroll on any viewport; anchor scroll lands below navbar

- [ ] **Step 1: Walk every section at 390px (iPhone 12) in DevTools**

Open DevTools → Device Toolbar → iPhone 12. Scroll the full page. Log any of these issues:
- Horizontal scrollbar visible → add `overflow-x: hidden` to the offending element
- Text too small to read → increase `clamp()` minimum
- Images stretching outside container → add `max-width: 100%`
- Buttons too narrow to tap → ensure `padding` gives ≥ 44px height

Fix each issue found. Common example fix for chip overflow:

```css
@media (max-width: 767px) {
  .skills-group { flex-direction: column; }
  .skills-label { min-width: unset; }
}
```

- [ ] **Step 2: Walk every section at 768px (iPad Mini) in DevTools**

Switch to iPad Mini. Verify research grid, project cards, and leadership grid all look good at this width. The `auto-fill minmax()` grids handle this automatically, but verify no cards are either too wide or too narrow.

- [ ] **Step 3: Verify all anchor links scroll correctly**

Click every nav link (About, Research, Experience, Leadership, Beyond the Lab, Contact). For each: the section heading must be fully visible below the navbar, not partially hidden behind it. The `scroll-padding-top: var(--nav-height)` in the reset handles this — if any heading is hidden, check that the `<section id="...">` is the direct scroll target (not a child element).

- [ ] **Step 4: Run Lighthouse Accessibility audit**

Chrome DevTools → Lighthouse tab → Accessibility only → Analyze page load. 

Target: ≥ 90. Common fixes:
- If images have empty `alt`: add descriptive alt text
- If contrast fails on chip text: the chip colors already use darkened tones (`#007a65`, `#b5400c`, `#0d4899`) for this reason — re-check if still flagged
- If form controls are missing labels: N/A (no forms on this site)

- [ ] **Step 5: Test in Firefox**

Open in Firefox. Verify: CSS Grid layouts render correctly, Google Fonts load, SVG data-URI patterns display, smooth scroll works.

- [ ] **Step 6: Commit**

```bash
git add index.html style.css
git commit -m "fix: responsive polish, accessibility audit fixes, and cross-browser check"
```

---

## Task 12: GitHub Pages Deployment

**Files:**
- No code changes — this task covers repository creation, push, and live site verification

**Interfaces:**
- Consumes: all files in `personalwebsite/`
- Produces: live public URL; placeholders swapped for real photos

- [ ] **Step 1: Create a GitHub account if needed**

Go to github.com. Sign up or sign in.

- [ ] **Step 2: Create a new repository**

Click the + icon → New repository.
- Repository name: `personalwebsite` (site will be at `https://<yourusername>.github.io/personalwebsite/`)
  — OR — name it `<yourusername>.github.io` for a root site at `https://<yourusername>.github.io/`
- Visibility: **Public** (required for free GitHub Pages)
- Do NOT check "Add a README file" (files already exist locally)
- Click **Create repository**

- [ ] **Step 3: Initialize git and push from the local directory**

```bash
git init
git add .
git commit -m "initial: Malaika Wanjihia personal website"
git branch -M main
git remote add origin https://github.com/<yourusername>/personalwebsite.git
git push -u origin main
```

Replace `<yourusername>` with your actual GitHub username.

- [ ] **Step 4: Enable GitHub Pages**

On GitHub: go to the repository → **Settings** → **Pages** (left sidebar) → Source → **Deploy from a branch** → Branch: `main` → Folder: `/ (root)` → **Save**.

Wait ~60 seconds for the first build, then visit `https://<yourusername>.github.io/personalwebsite/`.

- [ ] **Step 5: Verify the live site**

Check the live URL for: hero loads with pattern, Google Fonts load (Playfair Display name is distinctive), nav is sticky, all anchor links work, LinkedIn and Etsy links open in new tab, no broken image icons (placeholder backgrounds should show instead).

- [ ] **Step 6: Add real photos**

Copy your five photo files into `assets/images/` with these exact filenames:
- `portrait.jpg` — smiling portrait (used in hero)
- `equator-kenya.jpg` — equator photo (used in about + beyond the lab)
- `skydiving.jpg` — skydiving photo
- `hat.jpg` — hat photo
- `cheetah-baby.jpg` — baby cheetah photo

Then push:

```bash
git add assets/images/
git commit -m "feat: add personal photos"
git push
```

- [ ] **Step 7: Personalize the hero tagline and bio text**

The hero tagline in Task 4 and the About bio in Task 5 are written as drafts. Open `index.html` in any text editor, search for the text, and rewrite in your own voice. Push the changes:

```bash
git add index.html
git commit -m "content: personalize hero tagline and about bio"
git push
```
