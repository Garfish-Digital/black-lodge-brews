# Black Lodge Brews — Update Roadmap

This document is written for Sonnet to execute. Follow each step in order. Do not add features or make changes beyond what is specified.

---

## Phase 1 — Palette & Variable Updates

### Step 1.1 — `src/styles/_variables.scss`

Make the following changes:

1. **Remove** the line: `$primary-brown: #3E2723;`
2. **Update** `$amber-gold` from `#FFA000` to `#C8920A`
3. **Add** two new variables directly below the `$roasted-malt` line:
   ```scss
   $deep-ember: #2A1208;
   $charred-oak: #0F0B09;
   ```

Final color block should read:
```scss
$roasted-malt: #1A0E0A;
$deep-ember: #2A1208;
$charred-oak: #0F0B09;
$foam-cream: #F5E6D3;
$dark-forest: #1B3A1B;
$amber-gold: #C8920A;
$beer-foam: #FFFBF0;
```

---

## Phase 2 — Section Background Gradients

### Step 2.1 — `src/styles/global.scss` — Body background

Replace the body background:
```scss
// OLD
background: linear-gradient(135deg, $roasted-malt 0%, $primary-brown 100%);

// NEW
background:
  radial-gradient(ellipse at 20% 80%, rgba(27, 58, 27, 0.2) 0%, transparent 55%),
  radial-gradient(ellipse at 80% 20%, rgba(42, 18, 8, 0.35) 0%, transparent 50%),
  $charred-oak;
```

### Step 2.2 — `src/styles/_home-page.scss` — Hero background

Replace the `.hero` background:
```scss
// OLD
background: linear-gradient(135deg, $roasted-malt 0%, $primary-brown 50%, $dark-forest 100%);

// NEW
background:
  radial-gradient(ellipse at 15% 72%, rgba(27, 58, 27, 0.5) 0%, transparent 55%),
  radial-gradient(ellipse at 82% 18%, rgba(42, 18, 8, 0.75) 0%, transparent 50%),
  $charred-oak;
```

### Step 2.3 — `src/styles/_home-page.scss` — Taps section background

Replace the `.taps-section` background:
```scss
// OLD
background: linear-gradient(135deg, $primary-brown 0%, $roasted-malt 100%);

// NEW
background:
  radial-gradient(ellipse at 88% 8%, rgba(200, 146, 10, 0.1) 0%, transparent 50%),
  radial-gradient(ellipse at 12% 92%, rgba(42, 18, 8, 0.55) 0%, transparent 45%),
  $roasted-malt;
```

### Step 2.4 — `src/styles/_home-page.scss` — Taproom section background

Replace the `.taproom-section` background:
```scss
// OLD
background: linear-gradient(135deg, $roasted-malt 0%, $primary-brown 100%);

// NEW
background:
  radial-gradient(ellipse at 6% 94%, rgba(27, 58, 27, 0.4) 0%, transparent 52%),
  radial-gradient(ellipse at 94% 6%, rgba(42, 18, 8, 0.45) 0%, transparent 48%),
  #12080A;
```

---

## Phase 3 — Typography & Accent Color Updates

### Step 3.1 — `src/styles/_home-page.scss` — Hero title text gradient

Replace the `.hero-title` background gradient:
```scss
// OLD
background: linear-gradient(45deg, $amber-gold, #FFD54F);

// NEW
background: linear-gradient(25deg, #C8920A, #E8B84B);
```

### Step 3.2 — `src/styles/_home-page.scss` — Hero beer liquid fill color

In `.beer-liquid`, update both rgba values from old amber to new gold:
```scss
// OLD
background: linear-gradient(to top,
  rgba(255, 160, 0, 0.5) 0%,
  rgba(255, 213, 79, 0.35) 100%);

// NEW
background: linear-gradient(to top,
  rgba(200, 146, 10, 0.5) 0%,
  rgba(232, 184, 75, 0.35) 100%);
```

### Step 3.3 — `src/styles/_home-page.scss` — Card hover sweep line color

The `.feature, .event-item` shared `::before` pseudo-element has a hardcoded old amber value. Update it:
```scss
// OLD
background: linear-gradient(90deg, transparent, rgba(255, 160, 0, 0.7), transparent);

// NEW
background: linear-gradient(90deg, transparent, rgba(200, 146, 10, 0.7), transparent);
```

---

## Phase 4 — Card Hover Background

### Step 4.1 — `src/styles/_home-page.scss` — Card hover state

In the shared `.feature, .event-item` block, replace the flat hover background with a radial gradient:
```scss
// OLD
&:hover {
  background: rgba(245, 230, 211, 0.06);

  &::before {
    transform: scaleX(1);
  }
}

// NEW
&:hover {
  background:
    radial-gradient(ellipse at 50% 0%, rgba(200, 146, 10, 0.08) 0%, transparent 65%),
    rgba(245, 230, 211, 0.05);

  &::before {
    transform: scaleX(1);
  }
}
```

---

## Phase 5 — Animated Grain Overlay

### Step 5.1 — Create `src/components/GrainOverlay.astro`

Create this file with the following content exactly:

```astro
---
// GrainOverlay — Animated warm film grain, fixed overlay across all sections.
// Draws on a tiled 256x256 off-screen canvas for performance.
// Throttled to ~30fps. Pauses when tab is not visible.
---

<canvas id="grain-overlay"></canvas>

<style>
  #grain-overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    pointer-events: none;
    z-index: 9997;
  }
</style>

<script>
  const canvas = document.getElementById('grain-overlay') as HTMLCanvasElement;
  const ctx = canvas.getContext('2d')!;

  const offscreen = document.createElement('canvas');
  offscreen.width = 256;
  offscreen.height = 256;
  const oCtx = offscreen.getContext('2d')!;

  let frameCount = 0;
  let isVisible = true;

  function resize() {
    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;
  }

  function drawGrain() {
    // 2x2px grain particles for a natural, film-like clump
    const gsize = 2;
    const imageData = oCtx.createImageData(256, 256);
    const data = imageData.data;

    for (let y = 0; y < 256; y += gsize) {
      for (let x = 0; x < 256; x += gsize) {
        // Sparse — roughly half the particles are fully transparent
        const alpha = Math.random() > 0.5 ? Math.floor(Math.random() * 10) : 0;
        for (let dy = 0; dy < gsize; dy++) {
          for (let dx = 0; dx < gsize; dx++) {
            const i = ((y + dy) * 256 + (x + dx)) * 4;
            data[i]     = 255; // R — warm amber tint
            data[i + 1] = 215; // G
            data[i + 2] = 160; // B
            data[i + 3] = alpha;
          }
        }
      }
    }

    oCtx.putImageData(imageData, 0, 0);

    const pattern = ctx.createPattern(offscreen, 'repeat')!;
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    ctx.fillStyle = pattern;
    ctx.fillRect(0, 0, canvas.width, canvas.height);
  }

  function animate() {
    if (!isVisible) return;
    frameCount++;
    if (frameCount % 2 === 0) drawGrain(); // ~30fps
    requestAnimationFrame(animate);
  }

  document.addEventListener('visibilitychange', () => {
    isVisible = !document.hidden;
    if (isVisible) {
      frameCount = 0;
      animate();
    }
  });

  window.addEventListener('resize', resize);

  resize();
  animate();
</script>
```

### Step 5.2 — `src/layouts/BaseLayout.astro`

1. Import the component in the frontmatter:
   ```astro
   import GrainOverlay from '../components/GrainOverlay.astro';
   ```
2. Place `<GrainOverlay />` as the first child inside `<body>`, before any other content.

---

## Phase 6 — Cleanup

### Step 6.1 — Verify no remaining `$primary-brown` references

Search the entire `src/` directory for any remaining `$primary-brown` usages. If found in files not already edited above (e.g. component files), replace with the contextually appropriate new variable:
- Used as a dark background gradient stop → use `$deep-ember`
- Used as a near-black base color → use `$roasted-malt`

If no remaining references are found, no action needed.

---

## What Is Not In Scope For This Phase

- Story section background overlay (leave as-is)
- Contact section background overlay (leave as-is)
- `$shadow-*` variables (leave as-is)
- BeerCard component animations and hover states (separate phase)
- Navigation styling (separate phase)
- Any content changes
