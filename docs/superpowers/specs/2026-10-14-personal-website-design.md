# Personal Website — Design Spec
**Date:** 2026-10-14  
**Owner:** Malaika Wanjihia  
**Platform:** GitHub Pages  

---

## Intent & Success Criteria

A single-page personal website that serves as a professional and personal introduction for Malaika Wanjihia — PhD student in Human-Centered Computing at UMBC and Electrical Engineer at JHU APL. Success means:

- Visitors can immediately understand who Malaika is, what she researches, and what drives her
- The site feels vibrant and personal, not a dry academic CV
- Navigation is instant — anchor links jump to any section without a page load
- Works on mobile and desktop
- Deployable to GitHub Pages with zero build step

---

## Tech Stack

| Concern | Choice | Reason |
|---------|--------|--------|
| Structure | Single `index.html` | No build step; GitHub Pages serves directly |
| Styles | `style.css` | Full design control for custom patterns |
| Behavior | `script.js` | Smooth scroll, mobile nav toggle |
| Fonts | Google Fonts (CDN) | Free, no install |
| Hosting | GitHub Pages | Free for students |
| Images | `assets/images/` | Local — no external image host dependency |

No frameworks, no build pipeline, no dependencies beyond Google Fonts.

---

## File Structure

```
personalwebsite/
├── index.html
├── style.css
├── script.js
└── assets/
    └── images/
        ├── portrait.jpg          # smiling portrait → hero + about
        ├── equator-kenya.jpg     # equator photo → about section
        ├── skydiving.jpg         # skydiving → beyond the lab
        ├── hat.jpg               # hat photo → beyond the lab
        └── cheetah-baby.jpg      # baby with cheetah → beyond the lab
```

---

## Color System

| Token | Hex | Usage |
|-------|-----|-------|
| `--blue` | `#1565C0` | Nav, headings, hero background anchor |
| `--coral` | `#FF6B35` | Buttons, highlights, hero pattern accent |
| `--teal` | `#00C9A7` | Research section, skill tags |
| `--gold` | `#FFCA28` | Leadership section, decorative accents |
| `--magenta` | `#E91E8C` | Beyond the Lab section |
| `--cream` | `#FFF8F0` | Page background |
| `--charcoal` | `#1A1A2E` | Body text |

All colors defined as CSS custom properties on `:root`. Each major section uses one accent color as its thematic color.

---

## Typography

| Role | Font | Weight |
|------|------|--------|
| Name / hero heading | Playfair Display | 700 |
| Section headings | Raleway | 700 |
| Body / descriptions | Inter | 400, 500 |

Loaded via Google Fonts CDN. Fallbacks: Georgia → serif (Playfair), sans-serif (Raleway, Inter).

---

## Pattern System

Ankara-inspired geometric patterns implemented as:
- **CSS/SVG patterns:** diamonds, triangles, chevrons, zigzags
- **Hero section:** full-width background pattern in `--blue` + `--coral`
- **Section dividers:** thin geometric band between content blocks (color rotates per section)
- **Card accents:** geometric corner decoration on experience/research cards
- **Alternating sections:** light cream vs. lightly tinted backgrounds to create rhythm

Patterns are decorative only — they never reduce text readability. All text on pattern backgrounds uses sufficient contrast (WCAG AA minimum).

---

## Navigation

- **Sticky top navbar** — stays visible on scroll
- **Links:** About · Research · Experience · Leadership · Beyond the Lab · Contact
- **Behavior:** smooth scroll to anchor on click; active link highlights as user scrolls past each section
- **Mobile:** hamburger icon collapses nav into a dropdown; tap outside to close
- **No "Hero" label** — the hero section is unlabeled; nav starts from About

---

## Sections

### 1. Hero (unlabeled, top of page)
- Full-width Ankara geometric pattern background (`--blue` + `--coral`)
- Portrait photo (circular crop, right side on desktop / centered on mobile)
- Name: "Malaika Wanjihia" in Playfair Display, large
- Dual titles: "PhD Student · Human-Centered Computing @ UMBC" and "Electrical Engineer · JHU Applied Physics Laboratory"
- Short tagline (1 sentence — to be written, reflecting research + personality)
- Two CTA buttons: *My Research* (scrolls to `#research`) and *Say Hello* (scrolls to `#contact`)

### 2. About (`#about`)
- Accent color: `--blue`
- Kenya equator photo (left side on desktop / top on mobile)
- 3–4 sentence bio: from Kenya, passion for HCC and human-AI interaction, brief personality note
- Highlight badges: UMBC PhD · JHU MS · Morgan State BS · JHU APL Engineer
- Top Secret Clearance badge: **included** (owner's decision — it's a professional credential)

### 3. Research (`#research`)
- Accent color: `--teal`
- Current PhD focus: trust calibration in LLMs among vulnerable populations; agency and autonomy in human-AI interaction
- Secondary research: privacy concerns and physical impacts of data workers supporting humanoid robotic systems
- Two research cards with brief descriptions
- Placeholder row: "Publications & Presentations — coming soon" (updates as work accumulates)

### 4. Experience (`#experience`)
- Accent color: `--coral`
- JHU Applied Physics Laboratory (Jan 2021 – Present)
  - Project cards: HAYSTAC, Athena.AI, Proof Positive, DoD Health Analytics, Public Health Dashboard, UNO, Cross-Medium Comms, IAP, Miniature RF Devices
  - Each card: project name + 1–2 sentence description
- Lockheed Martin (2018–2021): single card, FPGA/F-35/Hypersonics work
- Skills section: color-tagged chips grouped by domain (AI/ML, Systems, Hardware, Design)
- Education timeline: UMBC PhD (in progress) · JHU MS 2023 · Morgan State BS 2020

### 5. Leadership & Service (`#leadership`)
- Accent color: `--gold`
- Four cards:
  - BFSA Caucus Chair — 80+ attendee events, JHU/APL partnership
  - BRIDGE Mentoring Program Chair — 25 mentors/mentees, 40% internship offer rate
  - Instrument Coordinator — 180 staff, 50+ instruments
  - Mentor & Community Builder — students and interns
- This section gets a geometric pattern divider above and below to set it apart

### 6. Beyond the Lab (`#personal`)
- Accent color: `--magenta`
- Photo grid (2×2 or 2×3): skydiving, hat, cheetah baby, Kenya equator (alternate use), travel/food if additional photos provided
- Each photo has a short caption (e.g., "Skydiving at 15,000 ft", "Baby me with a cheetah in Kenya 🐆")
- Hat business card: photo, short blurb, *Shop Malaika's Hats* button → Etsy link
- Short blurbs: travel, trying new foods, adrenaline, service/volunteering

### 7. Contact (`#contact`)
- Accent color: `--blue`
- Email: wanjihiam16@gmail.com (mailto link)
- LinkedIn: https://www.linkedin.com/in/malaika-wanjihia-302a06141/
- Etsy: https://www.etsy.com/shop/MalaikasHats
- Simple icon row — no contact form (avoids server-side complexity)
- Geometric pattern footer with name and "Built with ♥" line

---

## Responsiveness

- **Desktop (≥1024px):** Two-column layouts for hero, about, photo grid
- **Tablet (768–1023px):** Reduced padding, some two-column layouts preserved
- **Mobile (<768px):** Single column throughout, hamburger nav, full-width photos
- All font sizes use `clamp()` for fluid scaling

---

## Assumptions

- User will provide actual photo files for the `assets/images/` directory
- LinkedIn URL confirmed; no GitHub link on site
- Top Secret Clearance intentionally included on public site (owner confirmed)
- No contact form — mailto link is sufficient
- No analytics initially — can add later without redesign
