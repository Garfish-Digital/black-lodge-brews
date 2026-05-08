# Black Lodge Brews — Final Polish Roadmap

This document is written for Sonnet to execute. Follow each step in order. Do not add features or make changes beyond what is specified. Each phase ends in a runnable state — feel free to spot-check `npm run dev` between phases.

The previous `UPDATE_ROADMAP.md` is complete. This file covers the final polish pass identified during evaluation.

---

## Phase 1 — Residual Palette Sweep

The Phase 1–4 palette work in the previous roadmap missed several elements. They use the pre-rework `#FFA000` and `#FFD54F` accents and visibly clash with the new `#C8920A` / `#E8B84B` palette.

### Step 1.1 — `src/layouts/BaseLayout.astro` — Vertical navbar

This is the most visible element on the site. Update all five accent uses in the `.vertical-navbar .nav-link` block.

```scss
// OLD
border: 1px solid #FFA000;
transition: all $transition-fast;
text-align: center;
backdrop-filter: blur(10px);

&:hover {
  background: rgba(255, 160, 0, 0.2);
  color: $amber-gold;
  border-color: rgba(255, 160, 0, 0.5);
}

&.active {
  background: rgba(255, 160, 0, 0.3);
  color: $amber-gold;
  border-color: $amber-gold;
  box-shadow: 0 0 10px rgba(255, 160, 0, 0.3);
}

// NEW
border: 1px solid $amber-gold;
transition: all $transition-fast;
text-align: center;
backdrop-filter: blur(10px);

&:hover {
  background: rgba(200, 146, 10, 0.2);
  color: $amber-gold;
  border-color: rgba(200, 146, 10, 0.5);
}

&.active {
  background: rgba(200, 146, 10, 0.3);
  color: $amber-gold;
  border-color: $amber-gold;
  box-shadow: 0 0 10px rgba(200, 146, 10, 0.3);
}
```

### Step 1.2 — `src/styles/global.scss` — Link & button hover

Two replacements of the same hex.

```scss
// OLD
a {
  color: $amber-gold;
  text-decoration: none;
  transition: color $transition-fast;

  &:hover {
    color: #FFD54F;
  }
}

// NEW
a {
  color: $amber-gold;
  text-decoration: none;
  transition: color $transition-fast;

  &:hover {
    color: #E8B84B;
  }
}
```

```scss
// OLD
.btn {
  /* …unchanged… */
  &:hover {
    background: #FFD54F;
    transform: translateY(-2px);
    box-shadow: $shadow-lg;
  }

// NEW
.btn {
  /* …unchanged… */
  &:hover {
    background: #E8B84B;
    transform: translateY(-2px);
    box-shadow: $shadow-lg;
  }
```

### Step 1.3 — `src/pages/index.astro` — Contact section SVG fills

There are three SVGs (Location, Phone, Hours) with hardcoded `fill="#FFD54F"`. Replace each occurrence:

```astro
// OLD
fill="#FFD54F"

// NEW
fill="#C8920A"
```

Use `replace_all` if your editor supports it — there are exactly three matches in this file.

### Step 1.4 — `src/pages/index.astro` — Mist firefly color

```astro
// OLD
<MistParticlesFireflies
  particleCount={10}
  color="#FFA000"
  size={1}
  speed={0.05}
  opacity={0.3}
/>

// NEW
<MistParticlesFireflies
  particleCount={10}
  color="#C8920A"
  size={1}
  speed={0.05}
  opacity={0.3}
/>
```

### Step 1.5 — `src/components/BeerCard.astro` — Default beer color

Two occurrences in this file:

```ts
// OLD (line ~19, prop default)
color = '#FFA000',

// NEW
color = '#C8920A',
```

```ts
// OLD (line ~376, JS fallback)
this.color = element.dataset.color || '#FFA000';

// NEW
this.color = element.dataset.color || '#C8920A';
```

(The per-beer colors in the `beers` array in `index.astro` are intentional brand colors per beer — leave those alone.)

---

## Phase 2 — Beer Card Status Badges

The current "On Tap" green and "Coming Soon" purple read as cartoonish against the dark luxury palette. Replace with a monochromatic amber/neutral pairing.

### Step 2.1 — `src/components/BeerCard.astro` — `.beer-status` block

```scss
// OLD
.beer-status {
  .status-available {
    background: rgba(27, 58, 27, 0.8);
    color: #4CAF50;
    padding: $spacing-xs $spacing-sm;
    border-radius: $border-radius-sm;
    font-size: 0.8rem;
    font-weight: 600;
    border: 1px solid rgba(76, 175, 80, 0.3);
  }

  .status-unavailable {
    background: rgba(138, 43, 226, 0.2);
    color: #BA68C8;
    padding: $spacing-xs $spacing-sm;
    border-radius: $border-radius-sm;
    font-size: 0.8rem;
    font-weight: 600;
    border: 1px solid rgba(186, 104, 200, 0.3);
  }
}

// NEW
.beer-status {
  .status-available,
  .status-unavailable {
    display: inline-block;
    padding: $spacing-xs $spacing-sm;
    border-radius: $border-radius-sm;
    font-size: 0.7rem;
    font-weight: 600;
    letter-spacing: 0.14em;
    text-transform: uppercase;
  }

  .status-available {
    background: rgba(200, 146, 10, 0.1);
    color: $amber-gold;
    border: 1px solid rgba(200, 146, 10, 0.35);
  }

  .status-unavailable {
    background: rgba(245, 230, 211, 0.03);
    color: rgba(245, 230, 211, 0.5);
    border: 1px solid rgba(245, 230, 211, 0.12);
  }
}
```

---

## Phase 3 — Footer Year

### Step 3.1 — `src/layouts/BaseLayout.astro`

```html
<!-- OLD -->
<p>&copy; 2024 Black Lodge Brews. All rights reserved.</p>

<!-- NEW -->
<p>&copy; 2026 Black Lodge Brews. All rights reserved.</p>
```

---

## Phase 4 — Remove Dead Code & Unused Assets

A significant amount of code was orphaned during the rework. Removing it shrinks page weight by ~850 KB and eliminates future palette-drift risk.

### Step 4.1 — Remove unused JS libraries from `<head>`

`src/layouts/BaseLayout.astro` — these libraries exist only for the never-instantiated `RippleEffect` class.

```html
<!-- OLD -->
<!-- Three.js and html2canvas for ripple effects -->
<script is:inline src="/libs/three.min.js"></script>
<script is:inline src="/libs/html2canvas.min.js"></script>

<!-- NEW (delete entirely — including the comment) -->
```

### Step 4.2 — Delete the library files themselves

Delete:
- `public/libs/three.min.js`
- `public/libs/html2canvas.min.js`

If `public/libs/` is now empty after deletion, also remove the directory.

### Step 4.3 — Remove unused web fonts

`src/layouts/BaseLayout.astro` — `Oswald` and `Alfa Slab One` are no longer referenced anywhere in styles.

```html
<!-- OLD -->
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Oswald:wght@400;500;600;700&family=Alfa+Slab+One&display=swap" rel="stylesheet">

<!-- NEW (drop the Google Fonts <link>; keep the preconnects — Inter & Playfair Display load via @import in global.scss and benefit from them) -->
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
```

### Step 4.4 — Remove the `RippleEffect` class

`src/pages/index.astro` — the entire class definition (~200 lines) is dead. Locate the block that begins:

```ts
// Three.js WebGL Ripple Effect for Feature Cards
class RippleEffect {
```

…and ends at the closing `}` of that class (immediately before `// Re-initialize on Astro page transitions`). Delete the comment line and the entire class. Approximate line range: 348–549.

### Step 4.5 — Delete `LiquidTransition` (orphaned component)

The component is imported and rendered but its `trigger()` method is never called.

1. Delete the file `src/components/LiquidTransition.astro`.
2. In `src/pages/index.astro`, remove the import:
   ```astro
   // OLD
   import LiquidTransition from "../components/LiquidTransition.astro";

   // NEW (delete line)
   ```
3. In `src/pages/index.astro`, remove the usage:
   ```astro
   // OLD
   <LiquidTransition direction="up" duration={0.8} color="#FFA000" />

   // NEW (delete line)
   ```

### Step 4.6 — Delete `PourAnimation` (orphaned component)

Delete the file `src/components/PourAnimation.astro`. It is not imported anywhere.

### Step 4.7 — Remove `liquid-page-transition` machinery

`src/layouts/BaseLayout.astro` — the div is rendered, the function is defined, neither is invoked.

```html
<!-- OLD (remove this line just inside <body>) -->
<div class="liquid-page-transition" id="pageTransition"></div>
```

In the same file's `<script>` block, remove the `triggerPageTransition` function:

```js
// OLD (delete entire function and its banner comment)
// Liquid page transition effect
function triggerPageTransition() {
  const transition = document.getElementById('pageTransition');
  if (transition) {
    transition.classList.add('active');
    setTimeout(() => {
      transition.classList.remove('active');
    }, 800);
  }
}
```

### Step 4.8 — Remove `ripple-overlay` from BeerCard

`src/components/BeerCard.astro` — the element is rendered but never animated.

```astro
<!-- OLD (just before the closing </div> of .beer-card) -->
  <div class="ripple-overlay"></div>
</div>

<!-- NEW -->
</div>
```

Then remove the corresponding CSS block:

```scss
// OLD (delete entire block)
.ripple-overlay {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: radial-gradient(circle, rgba(255, 160, 0, 0.2) 0%, transparent 70%);
  opacity: 0;
  pointer-events: none;
  border-radius: $border-radius-lg;
}
```

### Step 4.9 — Remove unused `image` prop on BeerCard

`src/components/BeerCard.astro` — declared and destructured, never rendered.

```ts
// OLD
export interface Props {
  name: string;
  type: string;
  abv: number;
  ibu: number;
  description: string;
  color?: string;
  available?: boolean;
  image?: string;
}

const {
  name,
  type,
  abv,
  ibu,
  description,
  color = '#C8920A',
  available = true,
  image
} = Astro.props;

// NEW
export interface Props {
  name: string;
  type: string;
  abv: number;
  ibu: number;
  description: string;
  color?: string;
  available?: boolean;
}

const {
  name,
  type,
  abv,
  ibu,
  description,
  color = '#C8920A',
  available = true
} = Astro.props;
```

### Step 4.10 — Remove unused `.map-placeholder` CSS

`src/styles/_home-page.scss` — no markup uses this class. Delete the entire `.map-placeholder { … }` block within `.contact-section` (approximately lines 349–377).

### Step 4.11 — Remove the unused `rippleEffect` keyframe in BeerCard

`src/components/BeerCard.astro` — `@keyframes rippleEffect` is no longer referenced after Step 4.8.

```scss
// OLD (delete entire block)
@keyframes rippleEffect {
  0% {
    opacity: 0;
    transform: scale(0.8);
  }
  50% {
    opacity: 1;
    transform: scale(1);
  }
  100% {
    opacity: 0;
    transform: scale(1.2);
  }
}
```

(`@keyframes bubbleRise` is still used — leave it.)

---

## Phase 5 — Fix Broken Behaviors

### Step 5.1 — `src/components/MistParticlesBeer.astro` — Fix visibility pause

The visibility handler reads `container.mistParticlesBeer?.destroy()`, but the constructor never assigns the instance. Result: particles keep animating in background tabs, and a duplicate instance is created when the tab returns.

In the `init()` method, add the instance assignment to the container at the end:

```js
// OLD
init() {
  this.resize();
  this.createParticles();
  this.animate();

  window.addEventListener('resize', () => this.resize());
}

// NEW
init() {
  this.resize();
  this.createParticles();
  this.animate();

  window.addEventListener('resize', () => this.resize());

  this.container.mistParticlesBeer = this;
}
```

### Step 5.2 — `src/components/MistParticlesFireflies.astro` — Same fix

```js
// OLD
init() {
  this.resize();
  this.createParticles();
  this.animate();

  window.addEventListener('resize', () => this.resize());
}

// NEW
init() {
  this.resize();
  this.createParticles();
  this.animate();

  window.addEventListener('resize', () => this.resize());

  this.container.mistParticlesFireflies = this;
}
```

### Step 5.3 — `src/components/GrainOverlay.astro` — Restore intended grain spec

The current code has `gsize = 1` and `frameCount % 1 === 0`, which runs every frame at 1px noise (more flicker, 2× the CPU). Roadmap originally specified 2px clumps at ~30fps.

```js
// OLD
function drawGrain() {
  // 2x2px grain particles for a natural, film-like clump
  const gsize = 1;
  const imageData = oCtx.createImageData(256, 256);
  const data = imageData.data;

  for (let y = 0; y < 256; y += gsize) {
    for (let x = 0; x < 256; x += gsize) {
      // Sparse — roughly half the particles are fully transparent
      const alpha = Math.random() > 0.5 ? Math.floor(Math.random() * 8) : 0;

// NEW
function drawGrain() {
  // 2x2px grain particles for a natural, film-like clump
  const gsize = 2;
  const imageData = oCtx.createImageData(256, 256);
  const data = imageData.data;

  for (let y = 0; y < 256; y += gsize) {
    for (let x = 0; x < 256; x += gsize) {
      // Sparse — roughly half the particles are fully transparent
      const alpha = Math.random() > 0.5 ? Math.floor(Math.random() * 10) : 0;
```

```js
// OLD
function animate() {
  if (!isVisible) return;
  frameCount++;
  if (frameCount % 1 === 0) drawGrain(); // ~30fps
  requestAnimationFrame(animate);
}

// NEW
function animate() {
  if (!isVisible) return;
  frameCount++;
  if (frameCount % 2 === 0) drawGrain(); // ~30fps
  requestAnimationFrame(animate);
}
```

---

## Phase 6 — UX Polish

### Step 6.1 — `src/components/BeerCard.astro` — Faster stat animation

The 3-second number ramp feels sluggish for a luxury feel. Reduce to 1.4s.

```js
// OLD
this.animateNumber(statValue, 0, target, 3000, isABV);

// NEW
this.animateNumber(statValue, 0, target, 1400, isABV);
```

### Step 6.2 — `src/styles/global.scss` — Reduced-motion accessibility

Add this block at the end of the file (after the existing scrollbar rules):

```scss
@media (prefers-reduced-motion: reduce) {
  #grain-overlay,
  .mist-container-beer,
  .mist-container-fireflies {
    display: none;
  }

  .parallax-bg {
    transform: none !important;
  }

  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
}
```

---

## Phase 7 — Verification

After completing Phases 1–6, do the following:

1. **Search for any remaining old-palette references.** Run from the project root:
   ```sh
   grep -rn "FFA000\|FFD54F\|3E2723\|primary-brown" src/
   ```
   The output should be empty. If anything remains, replace the value with the contextually appropriate new color (`#C8920A` for accent, `#E8B84B` for lighter accent, `$roasted-malt` or `$deep-ember` for dark backgrounds).

2. **Search for any orphaned references to deleted components/files.** Run:
   ```sh
   grep -rn "RippleEffect\|LiquidTransition\|PourAnimation\|liquid-page-transition\|ripple-overlay\|three.min\|html2canvas" src/ public/
   ```
   The output should be empty.

3. **Build and preview:**
   ```sh
   npm run build
   npm run preview
   ```
   Confirm:
   - No console errors at page load
   - Hero beer fill animates as before
   - Mist particles + fireflies render
   - Grain overlay renders subtly (should look like soft warm static, not aggressive flicker)
   - Tap list cards animate stats from 0 to target in ~1.4s
   - Status badges render in amber (available) and muted neutral (coming soon)
   - Vertical navbar accent matches the new palette
   - Parallax in Story and Contact sections still smooth-scrolls
   - Toggle "Reduce motion" in OS settings → grain, mist, parallax all stop; layout remains intact

4. **Spot-check on mobile breakpoints** (Surface Duo portrait/landscape, iPad, 1440 desktop). Layout was previously verified — this pass should not have changed any layout primitives.

---

## What Is Not In Scope For This Phase

- BeerCard glass artwork or per-beer color values
- Hero fill animation timing curve
- Story / Contact background imagery
- Vertical navbar position or breakpoint behavior
- SCSS variable additions
- Footer content beyond the year
- Any new components or features
