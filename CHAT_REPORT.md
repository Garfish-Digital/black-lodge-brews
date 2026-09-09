# Black Lodge Brews — visual and functional improvement report

**Reviewed:** September 7, 2026  
**Goal:** Make the project feel more professional, expensive, and convincingly premium while preserving its woodland brewery identity.

## Recommendation

Your instinct is right: I would retire the opening beer-fill effect and replace the Taproom feature/event cards with a more editorial composition. Those changes should be part of a broader move toward **a warm, carefully designed lodge bar**: expressive photography, confident typography, a concise beer menu, and effortless visit planning.

The current implementation has a coherent atmosphere, but relies heavily on familiar decoration: gold gradients, translucent bordered panels, glowing hover effects, illustrated filling glasses, animated statistics, particles, and grain. Their repetition makes the experience feel like a themed website demonstration. A more expensive impression would come from showing the product, the place, and the care behind them, supported by fewer, more deliberate visual effects.

The strongest investment is **better art direction and information hierarchy**, followed closely by mobile usability and content accuracy. Merely reducing corner radii or choosing a more luxurious font will have limited impact.

## Review scope and confidence

I reviewed the Astro page, layout, all current components and styles, package configuration, README, project guidance, and existing final update roadmap. I inspected both supplied photographs directly and checked the generated production HTML/CSS. `npm run build` passed, with Sass `@import` deprecation warnings.

This is a source and asset review, not a completed live-browser audit. The Browser skill was consulted, but its required browser execution tool was unavailable in this session. I have not measured rendered contrast, browser performance, screen-reader output, or actual device layouts. Findings below distinguish confirmed implementation defects from visual judgments and risks requiring browser verification. No Lighthouse score or performance improvement percentage is claimed.

The project describes itself as a portfolio demo. Recommendations involving real locations, bookings, memberships, or business information apply only if those services actually exist. A polished demonstration can use clearly identified sample content without implying that a fictional business accepts real reservations.

## Confirmed direction and new hero images

**User direction, September 7, 2026:** Preserve the existing palette, parallax scrolling, and firefly particle system over the woodland image. Rework the hero completely. These are design constraints for subsequent work; suggestions elsewhere to simplify effects must not be interpreted as removing the retained parallax or woodland fireflies. Reduced-motion alternatives and lifecycle/performance fixes still apply.

I inspected both new images and verified their intrinsic dimensions:

| Image | Dimensions | Assessment |
| --- | --- | --- |
| `public/images/beer-1.avif` | 1200 × 855 | Usable for a contained desktop hero image and mobile composition. Not ideal as a crisp full-width desktop background. |
| `public/images/beer-2.jpg` | 600 × 331 | Too small for a large desktop hero. Suitable as a composition reference or a small supporting image. The file actually contains WebP data despite its `.jpg` extension. |

**Recommended candidate: beer-1, in a split composition.** Its amber beer, brown background, and timber fit the existing palette. Place the photograph in a roughly 500–600 CSS-pixel-wide panel and the heading/actions alongside it. At 600 CSS pixels wide, 1200 source pixels provide approximately 2× density before additional cropping. On mobile, place text above the photograph and retain enough of its original aspect ratio to avoid cutting off the glasses.

Both photographs place the beer on the left. If using text over the image, the right side is the natural location, but beer-1 has a bright amber area there that would require careful contrast treatment. A separate text column is the stronger starting point: it protects readability, uses the available resolution efficiently, and preserves the existing dark palette without obscuring the photograph.

Beer-2 has the simpler, moodier composition, with one glass and darker negative space. Its resolution is the limiting factor. At 600 CSS pixels wide it provides only 1× density; at 1440 pixels it would be enlarged 2.4× before considering high-density displays. Seek a larger original if that composition becomes the preferred direction.

For a full-width 1440–1920 CSS-pixel hero, explore an original approximately 2400–3200 pixels wide, with the final requirement determined by crop, display density, and compression. Neither supplied file offers that headroom. Enlarging the current files will not recover genuine detail. The 1200 × 855 image would also lose a substantial part of its vertical composition when cropped into a wide banner.

Aesthetic assessment: beer-1 is a useful working asset, but the two glasses, grain props, and bright amber backdrop read as traditional beer advertising. A carefully composed layout can elevate it; a more distinctive photograph could still improve the eventual result. Resolution alone does not determine whether an image feels luxurious.

**Updated direction:** The user prefers trying beer-1 full width. A full-width photographic hero is now implemented for evaluation, with a mirrored photograph, cream typography on the left, and a separate mobile crop/overlay. The hero tap-list link was removed at the user’s request. The original beer-fill animation is removed. The existing palette, woodland fireflies, and parallax remain. The resolution guidance above describes tradeoffs, not a prohibition on using the image full width. The earlier split-layout suggestion is superseded by this experiment.

## What is worth keeping

- **The underlying concept.** Woodland mystery, dark timber, amber beer, and a lodge setting form a distinctive basis for a hospitality brand.
- **The restrained palette already introduced.** Charred oak, roasted malt, cream, forest green, and muted amber are a good foundation. The next improvement should focus on how those colors are used.
- **The existing type pairing as a starting point.** Playfair Display and Inter can support this direction with better hierarchy and spacing. A font purchase is not a prerequisite.
- **The lightweight architecture.** Astro and static rendering suit this site. There is no design-driven reason to migrate frameworks.
- **Self-hosted fonts and WebP assets.** These are useful foundations, though delivery and stylesheet organization can improve.
- **Readable beer names, styles, and textual availability.** Preserve that useful information while simplifying its presentation.
- **The forest photograph.** Its texture supports the brand, although it cannot establish what the brewery or taproom actually looks like.

## Priorities at a glance

Effort is relative: **S** is a focused change, **M** spans a section or several files, and **L** includes substantial art direction, content, or integration work. These are planning estimates, not delivery commitments.

| Priority | Improvement | Why it matters | Effort |
| --- | --- | --- | --- |
| P0 | Render real ABV and IBU values in HTML | Beer information currently begins as incorrect zeros | S |
| P0 | Correct global style scope and reduced-motion behavior | Shared rules currently miss child components; hidden effects would still execute | M |
| P0 | Rework mobile spacing and navigation | Fixed side navigation and large internal padding threaten readability | M |
| P0 for a real launch | Replace sample visit details and undated events | Credibility depends on accurate, usable information | S–M |
| P1 | Replace hero fill with a composed photographic opening | Largest change to the first impression | M–L |
| P1 | Rebuild Taproom around an interior image and event list | Makes the experience tangible and removes repetitive boxes | M–L |
| P1 | Simplify beer cards into a menu | Improves product comparison and perceived refinement | M |
| P1 | Introduce clear visit and menu actions | Gives the atmosphere a practical purpose | S–M |
| P1 | Refine typography, spacing, and copy | Creates consistency across the whole site | M |
| P2 | Simplify or repair animation infrastructure | Reduces unnecessary work and lifecycle bugs | M |
| P2 | Improve image delivery and sharing metadata | Supports a polished production experience | S–M |
| P2 | Refresh documentation and content structure | Makes the improvements maintainable | S–M |

## 1. Replace the opening beer animation

### What the implementation does

In `src/pages/index.astro`, a timer waits two seconds before adding `.filling`. In `src/styles/_home-page.scss`, the beer level then transitions over four seconds to **18.75% of the hero height**. The effect spans the hero width; it does not fill the entire viewport. Foam has additional delayed transitions, and particles initialize after three seconds.

The hero otherwise consists of a three-line brand name and a short italic slogan. It has no visible primary action. Styles for `.hero-buttons` exist, but matching buttons are absent from the markup.

### Why I would remove it

The flat liquid band and foam are a literal illustration of beer, without the material detail that makes a real pour attractive. The delayed sequence introduces several changing elements before establishing a useful next step. It expresses playfulness more readily than the quiet confidence the new direction needs.

It is not a blocking preloader: visitors can still scroll. The issue is attention and aesthetic fit, rather than a claim that the site is unusable for six seconds.

### Preferred replacement

Use a strong still photograph with a controlled composition: a freshly poured glass on dark timber, a warmly lit bar, or a close view of a distinctive taproom material. Place the wordmark and short introduction in intentional negative space. Keep important typography as real HTML.

A suggested hierarchy:

1. Small factual label identifying the brewery and place, using verified information.
2. Black Lodge Brews as the dominant brand element.
3. One concise line that describes the experience.
4. Primary action: **Plan your visit**, linking to useful visit details.
5. Secondary action: **See the tap list**.
6. A compact location/hours line when those facts are verified.

A possible creative line is “Good beer. A place to linger.” This is a direction to explore, not a claim about the business. Keep the stacked name if it composes well at the target widths; it is not inherently dated.

Use little or no entrance motion. If motion adds value, limit it to a short opacity change or very small movement, with the content already available. A photograph should remain attractive before scripts run.

### Alternative if a pour remains important

Use a brief, well-produced close-up video with a strong poster image and a still reduced-motion experience. Treat video as an optional enhancement after the still design works. Account for download cost and playback controls; do not make it a prerequisite for seeing the brand or actions. Another option is a small user-triggered pour within a beer detail view, where the interaction has context.

**Acceptance:** The first frame communicates the brand, product, and next step; no decorative delay is needed to make the composition complete; the mobile crop leaves text and actions readable.

## 2. Replace the Taproom cards with a composition about the place

### What feels dated

The Taproom currently presents four feature boxes and four event boxes. Both use the same translucent surface, border, eight-pixel radius, gold line reveal, and radial hover glow. Each has `4rem` padding, while the grids also have `4rem` gaps.

The problem is not that cards or rounded corners are universally outdated. The same container treatment gives different kinds of information equal weight. “Fresh Pours” and an event invitation should not require the same visual structure. Decorative hover responses on otherwise static content also suggest more interaction than is available.

Most significantly, this section describes an atmosphere without showing the room.

### Proposed layout

On wide screens, use one generous interior photograph alongside a narrower text column. Give the image roughly three-fifths of the composition as an initial design study, then adjust to the actual photograph. Use a short heading, two or three sentences, and a few concise facts beneath a fine divider.

Replace the four feature panels with a compact list describing what a guest can expect: beer service, food, music, and any verified special offering. Remove generic claims such as “every detail matters” unless the surrounding content demonstrates those details.

Place **What's on at the Lodge** below this composition. Use a vertical list with aligned dates, event titles, start times, and a real action where appropriate. Thin horizontal rules are sufficient separation. One genuinely important event may have a larger photograph; the rest should remain easy to scan.

On phones, stack image, description, visit action, and events. Use compact date/title arrangements that leave sufficient room for long titles. Let content determine row height.

| Current element | Suggested treatment |
| --- | --- |
| “Immerse Yourself” and generic introduction | A direct heading about spending time at the Lodge |
| Four equally prominent feature cards | A short factual list, without independent backgrounds |
| Four event cards in a grid | A chronological list with actual dates and times |
| Hover glow and animated gold border | A modest hover/focus change on genuine links |
| No room image | A substantial interior photograph |
| “Private Reserve” membership claim | Explain eligibility and access, or remove until supported |

### Event functionality

The hardcoded events contain month/day strings only, with no year, time, ticket information, or link. On this review date, the July labels appear stale if interpreted as the current year; the missing year prevents determining their intended schedule.

Store full dates and times, render semantic `<time datetime="…">` elements, and distinguish free entry, booking required, and sold out where applicable. Hide or archive completed events and show an honest empty state when nothing is scheduled.

Because this is a static site, filtering events during the build alone will not remove them as time passes unless the site rebuilds. Choose a scheduled rebuild, a suitable data source, or a small progressive client update with an explicit timezone. Start with the simplest process the content owner can reliably maintain.

**Acceptance:** A visitor understands what the room is like, what is happening, when it happens, and how to attend. Static descriptions do not pretend to be buttons.

## 3. Make the beer selection feel like a considered menu

The Tap List is separate from the Taproom, but its visual treatment needs to evolve with it. `BeerCard.astro` repeats a small CSS glass, fill animation, bubble animation, stat counters, and another glowing panel for each beer.

I recommend a typography-led menu with six entries, initially in two columns on desktop and a single column on mobile. Show the name, style, short tasting notes, ABV, and availability. Include serving size and price only when supplied. IBU can be secondary information; it should not compete with the beer name or flavor description.

For example, using existing project data:

> **Dark Lodge IPA**  
> India Pale Ale · 6.8% ABV · 65 IBU  
> Citrus, pine, smoky finish.  
> On tap

Keep the full description available if it adds useful character, but prioritize the facts needed to choose. Separate coming-soon beers from the current list, or identify them clearly without reducing the opacity of all their text.

If product imagery is introduced, use a consistent photographic set with matching lighting, crop, and scale. One excellent featured beer image beside a text menu will be more convincing than six mismatched images. A bespoke illustration system could also work, but would require a coherent visual language beyond the current generic glasses.

### Confirmed functional defect: initial beer values are false

The generated HTML contains `0%` and `0` for every beer. JavaScript waits for intersection, delays another 500 milliseconds, and animates to the real values over two seconds. Without JavaScript, those numbers remain zero.

Render `{abv}%` and `{ibu}` directly in Astro and remove the counters. Alcohol strength is product information, not a statistic that benefits from counting upward. This also eliminates a reduced-motion problem and a class of observer/timer complexity.

The card uses `cursor: pointer` but has no click action or link. Either present it as static menu content or provide a meaningful detail link with keyboard behavior. Do not add links merely to justify the existing hover treatment.

**Acceptance:** All beer facts are correct in the initial HTML and remain readable with JavaScript disabled. A menu entry only looks clickable when it does something useful.

## 4. Build a disciplined visual system

### Color and surfaces

Keep the near-black wood tones and warm cream. Use amber as a selective accent on actions or small details. The current repeated amber headings, gradient title, borders, active glow, badges, and date numerals give the accent too many jobs.

Use mostly solid surfaces. Reserve a soft tonal gradient for a genuinely useful atmospheric area. Test one warm cream menu section as an alternative to an entirely dark page; it could provide contrast and a tactile printed-menu feeling, but should be judged in a composition study.

Avoid making small text faint to create refinement. Particularly inspect the event month at `0.6rem` and 40% cream, and unavailable beer text whose opacity is compounded by the parent card. These are contrast risks, not measured failures from this review. For ordinary text, target at least 4.5:1 contrast; qualifying large text has a 3:1 threshold. [W3C contrast guidance](https://www.w3.org/WAI/WCAG22/Understanding/contrast-minimum.html)

### Typography

Use the display serif for major headings and selected beer names. Use Inter for navigation, times, practical information, and most small labels. The present global rule assigns the serif to every heading, including minor utility headings, which reduces contrast in the hierarchy.

Start body copy around 16–18px, with comfortable line height and a readable measure of roughly 55–65 characters. Use a small, consistent size scale and fluid heading sizing. These are design starting points, not rigid requirements.

Reduce widespread italics and letter spacing. Italics can provide one deliberate editorial accent; repeated italic subtitles become a template signature. Reserve uppercase tracking for short labels. Inspect actual font weights: some styles request weights not supplied for Playfair, so verify the rendered result before expanding the font inventory.

### Spacing and alignment

Both Tap List and Taproom use `14rem` vertical padding. That is 224px per edge at a 16px root size. Their specific rules are not overridden by the general mobile `.section` padding rule. Beer cards also have `4rem 2rem` margins in addition to grid gaps and their own padding.

This accumulates whitespace without strengthening hierarchy. Replace it with shared responsive section spacing and let the parent layout own the gaps between items. As an initial direction, explore 80–128px section spacing on wide screens and 48–64px on phones, with smaller spacing inside related groups.

Align headings, paragraphs, images, menus, and actions to a common container. Preserve asymmetry only when it balances an image or another meaningful element. The hero and story currently reserve grid columns without matching visual content; reassess those layouts instead of carrying their empty space into the redesign.

The desktop hero rule includes `padding: 0 calc(2rem - 80px)`. At normal font sizes this requests negative horizontal padding, which cannot provide a valid negative gutter. Replace such adjustments with intentional container alignment.

## 5. Give photography a real role

I inspected the supplied images:

- `story-trees.webp` is a dense aerial forest photograph. It supports place and texture but does not show the brewery, its people, or its craft.
- `contact-beer-table.webp` shows a bright overhead group toast with large handled beer mugs. Its social warmth is useful, but the casual imagery and bright treatment differ from the intimate lodge direction.

The existing backgrounds have overlays around 80–90% opacity. Even good photography cannot establish much identity when it is heavily obscured. Separate text and imagery where practical, or use localized contrast protection instead of uniformly darkening the whole image.

For a future asset brief, prioritize a hero product or bar image, a wide taproom interior, a close detail of glass/timber/brass, and a brewer or brewing-process image. Maintain consistent warm highlights, natural beer colors, and deep but readable shadows. Avoid generic luxury props unrelated to the brewery.

For a real venue, show the actual space and product. For a fictional portfolio concept, illustrative assets should remain consistent with its demonstration status.

Use responsive image sizes and explicit dimensions. Make the hero image discoverable early and avoid lazy-loading it; defer below-the-fold media. The two original woodland/contact image files total about 691KB on disk, which is an asset inventory measurement rather than a measured network waterfall.

## 6. Improve navigation and visit planning

### Navigation

The fixed vertical navigation has very small `0.675rem` text, outlined buttons, and no dedicated mobile layout. It occupies the right side of the page while the content continues behind that region. The overlap risk needs live viewport verification, but the lack of a mobile adaptation is confirmed.

Use a compact horizontal header on desktop with a clear brand/home link and a visit action. On mobile, use an accessible menu button or a compact arrangement of the most useful links. Provide visible focus treatment and comfortably sized touch targets; 44px is a useful project design target.

Restore the normal scrollbar. Hiding it removes a familiar indicator of position and page length without providing meaningful brand value.

The navigation intercepts all fragment links and calls `scrollIntoView({ behavior: 'smooth' })`. It does not update the URL fragment or explicitly manage destination focus. Prefer native anchors with `scroll-margin-top` and optional CSS smooth scrolling. Keep active-section enhancement separate, and expose the active link with `aria-current="location"`. Include a skip link to the main content.

### Visit details

The contact area provides a sample-looking address and phone number as plain text. The Google Maps embed identifies Portland's Pearl District rather than a verified brewery listing. This should be resolved before representing the site as an operating business.

Provide verified hours, an actionable directions link, a telephone link, and a concise arrival note. Useful additional facts include food availability, accessibility, seating policy, transit or parking, and any restrictions guests need to know. Only publish confirmed details.

Keep the map secondary to those essentials. An optional click-to-load map can preserve a cleaner composition and reduce initial third-party activity. If the iframe stays, retain its existing title and lazy loading.

Do not add a reservation button unless an actual booking route or policy exists. A premium experience can be as simple as clearly explaining that walk-ins are welcome and how to inquire about groups, when true.

## 7. Make the writing specific and credible

The current copy repeatedly refers to mysteries, secrets, whispers, shadows, and enigmas. That reinforces the theme but supplies little evidence of brewing quality or hospitality.

Keep the mysterious character in the name, occasional headings, and imagery. Use the rest of the writing to explain flavor, process, ingredients, people, and the experience of visiting.

Examples to explore:

| Current wording | More direct direction |
| --- | --- |
| Our Woodsy Taps | On Tap at the Lodge |
| Each brew tells a story, each sip reveals a secret | A concise introduction to the actual selection |
| The Taproom Experience | Stay a While |
| Find Your Way to the Lodge | Visit the Lodge |
| Generic statements about exceptional craft | Verified ingredient, process, or brewer details |

Shorten the story substantially, then support it with one or two concrete facts and a relevant image. Do not invent awards, supplier relationships, scarcity, tasting expertise, or membership benefits to make the brand sound premium. If the founded-in-2023 story is fictional, handle it consistently with the portfolio concept.

## 8. Technical defects and quality improvements

These findings are based on the current source and generated output, not merely copied from the existing roadmap.

| Finding | Evidence and consequence | Recommended action |
| --- | --- | --- |
| Shared styles are compiled as component-scoped CSS | `global.scss` is imported inside scoped style blocks in both `BaseLayout.astro` and `index.astro`. Generated shared selectors carry parent scope attributes; BeerCard descendants and particle canvases have different attributes. | Import global styles once from the layout frontmatter, or use an explicit global style block. Keep component styles local. Recheck child resets, typography, and box sizing. |
| Reduced-motion selectors miss the actual canvases | Generated grain/mist selectors require layout/page scope attributes that the canvas/container markup does not have. | Correct global scope, then verify reduced motion against production output. |
| Reduced motion is incomplete even after fixing scope | CSS does not cancel JS counters, particle loops, timers, grain rendering, or forced smooth scrolling. | Check `matchMedia` before starting effects, handle changes, and stop/cancel work. Preserve a complete static presentation. |
| Particle instances are not retained on initial creation | Initial `new MistParticles…` calls do not assign the properties later used by visibility handlers. Returning to the tab can create another instance while the original remains registered. | Keep one owned instance per container with explicit start, stop, and destroy methods. Browser throttling is not application cleanup. |
| Particle resize cleanup cannot remove its listener | Registration and removal create different arrow functions. | Store a stable handler reference and use it for both operations. |
| Fractional animation speeds are parsed incorrectly | `parseInt` turns beer speed `1.5` into `1`; firefly speed `0.05` becomes zero and falls back to `0.25`. | Use finite-number validation and preserve meaningful zero values. |
| Grain runs every animation frame | `frameCount % 1 === 0` is always true, despite the comment claiming roughly 30fps. | Prefer a subtle static texture. If retained, throttle by elapsed time, not alternate frames, and pause when appropriate. |
| Duplicate Astro lifecycle registration | `index.astro` registers the same parallax controller on `astro:page-load` twice, plus a DOM-ready initializer; there is no cleanup. | Use one lifecycle strategy. Duplicate creation is conditional on that event being emitted; no client router is present now. |
| Parallax adds complexity for two backgrounds | The build emits a page script chunk of 114.97kB, 45.53kB gzip, containing the GSAP/ScrollTrigger imports. | Preserve the requested parallax, consolidate its initialization, and profile its cost. Do not treat this chunk as the entire page transfer size. |
| Background stacking is fragile | `.parallax-bg` uses `z-index: -1` without an explicit isolated section stacking context. | Verify visibility in-browser. Prefer isolated sections with a background layer at zero and content above it. |
| Mobile event space is extremely constrained | Each event retains 128px total horizontal padding, a date column, and a 32px gap even after the grid becomes one column. | Use responsive padding and redesign the date/title layout; test 320px widths and long titles. |
| Global CSS is duplicated | Production CSS contains 24 `@font-face` declarations for the 12 definitions in the shared file. | Establish one global import. Duplicate declarations do not automatically mean duplicate font downloads. |
| Build warnings identify stylesheet maintenance work | Sass reports deprecated `@import` usage. | Migrate to `@use`/`@forward` as part of stylesheet cleanup, with visual checks. |
| Documentation has drifted | README remains the Astro starter; CLAUDE.md describes removed components. | Document the actual components, content-editing process, commands, asset conventions, and demo status. |

Astro scopes component styles by default; its documentation distinguishes global styles and imported external stylesheets. The generated output confirms why that distinction matters here. [Astro styling documentation](https://docs.astro.build/en/guides/styling/)

Reduced-motion preferences should produce a deliberately reduced experience; simply hiding an animated canvas does not stop the JavaScript responsible for it. [MDN reduced-motion documentation](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/At-rules/@media/prefers-reduced-motion)

### Relationship to the existing roadmap

Several earlier cleanup items are already reflected in the project: muted amber, neutral availability badges, local fonts, removed legacy effect libraries/components, and a 2026 footer. They should not be proposed again as untouched work.

Other issues remain, including particle lifecycle bugs and the grain timing mismatch. The existing reduced-motion block is present but suffers from the scope and JavaScript limitations above. The beer counter currently runs for two seconds, not the earlier roadmap's proposed 1.4 seconds. This report recommends removing that counter entirely.

## 9. Production polish and maintainability

Keep beer data, events, and business details in small typed data modules or Astro content collections. Share the same hours/address source between contact and footer. Validate complete event dates and available beer states. A CMS is optional until an actual editing workflow requires it.

The layout currently includes `noindex, nofollow`. That is reasonable for a demo and should remain deliberate. For a real public launch, review indexing configuration, canonical URL, sitemap, Open Graph metadata, a designed sharing image, and a brewery-specific favicon/identity system. Add structured business/event data only when backed by accurate published information.

A successful Astro build is not a comprehensive type, accessibility, or runtime check. Add a repeatable check command and a small set of useful checks around real failure cases, especially initial beer values, event dates, and navigation. Avoid building a large testing framework just to support cosmetic changes.

## 10. Suggested delivery sequence

### First: establish an accurate, stable baseline

Render correct beer values; fix global CSS scope; implement complete reduced-motion behavior; address mobile padding/navigation; clarify sample events and visit information. Repair any animation ownership bugs in effects that remain during the redesign.

**Reviewable result:** Existing design remains recognizable but the content is reliable, shared rules apply consistently, and narrow screens have a credible layout.

### Second: approve one coherent visual direction

Create desktop and mobile compositions for the hero, one menu entry, and Taproom. Use the same photography treatment, typography, spacing, and action styles throughout. Keep the existing palette; refine its hierarchy through spacing, typography, and selective accents.

**Reviewable result:** A clear answer to what Black Lodge Brews should look and feel like before polishing every component.

### Third: implement the high-impact redesign

Replace the fill hero, introduce the visit/menu actions, rebuild Taproom with an interior photograph and event list, simplify the beer presentation, and rewrite the copy. Rationalize section spacing and remove effect code made unnecessary by the new composition.

**Reviewable result:** A complete page that feels intentionally designed from opening to footer.

### Fourth: verify and prepare for use

Optimize image crops/delivery, test keyboard and phone interactions, check contrast, verify content freshness, add appropriate sharing metadata, and refresh project documentation.

**Reviewable result:** A dependable portfolio showcase or a credible business site, according to the project's chosen purpose.

## 11. Acceptance checks for the redesign

| Area | Pass condition |
| --- | --- |
| First impression | Brand, beer, and visit/menu actions are clear without waiting for animation |
| Taproom | A meaningful image and concise information explain the experience; events have complete useful details |
| Beer information | Correct ABV/IBU and availability are present with JavaScript disabled |
| Mobile | No clipped content or navigation overlap at 320, 375/390, 768, and 1024px widths; short landscape screens also work |
| Typography | Long beer/event names wrap gracefully; reading remains comfortable at increased text size and browser zoom |
| Keyboard | Visible focus, working skip link, meaningful link destinations, and logical focus order |
| Motion | Reduced-motion mode avoids parallax, counters, particle/grain work, and forced smooth scrolling |
| Content trust | Demo content is clear, or real address/hours/events and action destinations are verified |
| Performance | Test real mobile conditions; target good Core Web Vitals rather than relying on a build success |
| Maintenance | One source for business facts; an explicit process keeps tap availability and events current |

For production monitoring, good Core Web Vitals targets are LCP at or below 2.5 seconds, INP at or below 200ms, and CLS at or below 0.1, assessed at the 75th percentile of visits. These are future validation targets, not results measured in this review. [Web Vitals guidance](https://web.dev/articles/vitals)

## Recommended first design move

Replace the beer-fill hero and Taproom card grid together, using a shared photographic and typographic direction. Pair that work with correct, immediately readable beer data and a clear visit action. Those changes would address your two concerns while making the whole experience more convincing as a premium brewery.
