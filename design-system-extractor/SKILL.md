---
name: design-system-extractor
description: Inspect a live website with Chrome DevTools MCP, download and organize its media assets locally, and reverse-engineer its visual language, layout architecture, media placement, and animation mechanics into a rigorous DESIGN_SYSTEM.md. Use when asked to analyze a website, reproduce its design style, extract its assets/animations, document UI patterns, or create a design system from a reference site.
---

# Design System Extractor

## Mission

Given a website URL, inspect the accessible website systematically, extract and save its reusable media assets into a structured local project folder, and produce a repository-ready `DESIGN_SYSTEM.md` that documents the site's observable design language, media placement rules, animation mechanics, and implementation specifications.

Do not merely describe the homepage. Inspect the full accessible site surface: page templates, navigation, representative routes, responsive states, reusable components, media wrappers, and meaningful interactions/animations. Separate verified facts from visual estimates and unknowns. Never invent measurements, fonts, colors, breakpoints, keyframes, or behavior.

## 1. Required preflight: verify Chrome DevTools MCP

Before inspecting the website:

1. Inspect the current model/agent tool inventory and determine whether an MCP server or tool group named `chrome-devtools-mcp`, `chrome-devtools`, or an unmistakable Chrome DevTools MCP equivalent is available.
2. Confirm that the tools are callable in this session. A mention in project documentation, a config file, or a user claim is not sufficient proof that the MCP is connected.
3. If available, use its documented tools to launch or attach to a browser, navigate, inspect DOM and computed styles, capture screenshots, inspect network-loaded assets/scripts/stylesheets where supported, and interact with the page.
4. Check available local file-system/shell tools so downloaded media assets and inline SVGs can be written directly into the project directory.
5. Do not assume tool names or argument schemas. Read the exposed tool descriptions/schema and use only the actual available tools.
6. If the MCP is absent or cannot be called:
   - Stop the browser-inspection workflow.
   - Tell the user clearly that `chrome-devtools-mcp` is not available in the current session.
   - Explain that the skill itself cannot install or activate an MCP server.
   - Ask the user to configure/connect the MCP and retry, or explicitly authorize an alternative inspection method.
   - Do not claim to have inspected the site and do not produce a supposedly extracted design system from guesses.
7. If only some capabilities are available, state the limitation and continue only where the available evidence supports it. Never imply that an unavailable capability was used.

## 2. Input and scope

Required input:
- The reference website URL.

Optional input:
- Specific pages or subdomains to include/exclude.
- Custom output directory for `DESIGN_SYSTEM.md` and extracted media assets (default: `./design-system-assets/`).
- Whether the site is public, authenticated, or staging.
- Target viewport(s), framework, or implementation target (CSS, Tailwind, React, Next.js, Flutter, etc.).
- Whether the user wants the whole public site or selected sections.

Default scope:
- The supplied origin and its publicly reachable same-site pages.
- Include linked subdomains only when they are clearly part of the same product/site and safe to inspect.
- Do not bypass authentication, paywalls, bot protections, or access controls.
- Do not submit purchases, send messages, create accounts, delete data, or trigger irreversible actions.
- Avoid repeatedly submitting forms. Use safe, reversible interactions to reveal states.
- Respect robots/access restrictions and reasonable request volume.

If the URL is missing, ask for it before starting. If scope is ambiguous but the URL is present, use the default scope and report it.

## 3. Build a site inventory before drawing conclusions

Use the browser and page evidence to map the site:

1. Open the supplied URL and record the final URL, page title, viewport, and any redirect.
2. Inspect the global shell:
   - Header/navigation
   - Logo and brand marks
   - Main content container
   - Footer
   - Floating/sticky elements
   - Cookie/privacy banners or other overlays
3. Discover routes from visible navigation, footer links, in-page links, sitemap, and other same-site links exposed by the site. Use `robots.txt` and `sitemap.xml` if accessible; treat them as discovery aids, not proof that every route is usable.
4. Group routes by page template rather than documenting every near-identical page independently. Typical templates include home, listing, detail, pricing, blog/article, authentication, dashboard, contact, and legal pages.
5. Inspect at least one representative page from every distinct template and any page with a visibly unique layout or component.
6. Record inaccessible, broken, redirected, or blocked routes separately. Do not silently omit them.
7. Maintain a route inventory with status and evidence.

Do not call a site “fully inspected” unless the discovered route inventory has been reviewed and the remaining uninspected routes or limitations are explicitly listed.

## 4. Inspect each representative page

For every selected route:

- Capture a full-page screenshot when supported.
- Inspect the DOM hierarchy and semantic structure.
- Inspect computed styles for representative elements, layout containers, and media wrappers.
- Identify repeated layout patterns, stacking contexts (`z-index`), and component boundaries.
- Inspect all media elements (`<img>`, `<picture>`, `<video>`, `<svg>`, `<canvas>`, Lottie players, CSS `background-image`) alongside their parent containers to record how they are sized, cropped (`object-fit`), masked, layered behind text, and loaded across viewports.
- Check whether styling is responsive and whether components or media assets change structure/crops across viewports.
- Inspect interactive states safely: hover, focus, active, expanded, selected, disabled, loading, validation, and open/closed states where present.
- Inspect animations and motion mechanics: CSS transitions, `@keyframes` rules, scroll-triggered effects, parallax, smooth scrolling, and JavaScript animation libraries.
- Record page-specific exceptions rather than forcing every page into one global rule.

At minimum, inspect:
- Desktop: 1440px wide (or nearest practical viewport).
- Tablet: approximately 768px wide.
- Mobile: approximately 390px wide.

If the browser tooling cannot set viewports, use available viewport controls or report the missing responsive evidence. Do not fabricate mobile behavior from desktop screenshots.

## 5. Evidence and measurement rules

For every extracted value, classify its evidence:

- **Verified** — read directly from DOM, computed CSS, stylesheet rules, loaded asset metadata, or an exposed source file.
- **Observed** — clearly visible in screenshots or interaction behavior, but not directly confirmed in source/style values.
- **Estimated** — approximated visually; provide a range or approximate value and say so.
- **Unknown** — could not be determined reliably.

Prefer exact computed values over visual estimates. When CSS variables/design tokens are exposed, record their names and values. When a font file or `@font-face` declaration is available, record the actual family and weights. Do not infer a font solely from appearance if it cannot be confirmed.

For colors:
- Sample/read computed values for actual elements (background, text, border, accent, buttons, overlays, etc.).
- Normalize colors to HEX where practical, retaining RGBA/HSLA alpha for transparency and backdrop filters.
- Note where a color is used and whether it is a linear/radial/conic/mesh gradient.
- Do not assume that visually similar colors are identical.

For spacing, fluid sizing, and dimensions:
- Inspect computed `margin`, `padding`, `gap`, `width`, `max-width`, `height`, `aspect-ratio`, and grid/flex properties.
- Check stylesheet rules for fluid math functions (`clamp()`, `min()`, `max()`, `calc()`) and viewport units (`vw`, `vh`, `dvh`, `svh`).
- Derive a spacing scale only if repeated values support it. Mark inferred scale steps as estimated.
- Distinguish viewport width, page container width, and content width.

For breakpoints:
- Prefer `@media` and `@container` query values found in CSS/source.
- Otherwise resize and observe the actual layout transition; report only the tested range, not an invented exact breakpoint.

For motion and animations (How it works):
- Do not merely describe the visual effect; extract the exact mechanical implementation:
  1. **Engine detection**: Inspect network requests, scripts, DOM attributes (`data-*`), and global window objects to identify libraries (e.g., GSAP/ScrollTrigger, Framer Motion, Lottie/dotLottie, Rive, Three.js/WebGL, Spline, Lenis/Locomotive Scroll, AOS, or pure CSS).
  2. **CSS Transitions & Keyframes**: Extract `transition-property`, `transition-duration`, `transition-timing-function` (including exact `cubic-bezier(...)` or `steps(...)` values), `transition-delay`, and the full `@keyframes` rule blocks (`0%` to `100%` states).
  3. **Mutating properties**: Record which exact properties are animating (e.g., `transform: translate3d() / scale() / rotate()`, `opacity`, `clip-path`, `filter`, `background-position`, or SVG `stroke-dasharray` / `stroke-dashoffset`).
  4. **Triggers & Choreography**: Document what drives the animation (page load, hover/focus, `IntersectionObserver` on enter-viewport, continuous scroll-linked progress, or cursor/mousemove coordinates) and any stagger delays between child elements.

## 6. Extract and organize media assets locally

Extract all key design media assets discovered during inspection and save them into a structured folder inside the current project workspace (default: `./design-system-assets/`, unless the user specifies another directory):

```text
./design-system-assets/
├── logos/          # Brand logos, wordmarks, favicons, app icons
├── icons/          # Extracted UI icons and inline SVGs saved as standalone .svg files
├── images/         # Hero visuals, product mockups, backgrounds, illustrations, textures
├── videos/         # Background loops, feature demos, poster frames (.mp4, .webm, etc.)
├── animations/     # Lottie JSON (.json / .lottie), Rive files, or canvas/shader assets
└── fonts/          # Custom webfont files (.woff2, .woff, .ttf) if publicly accessible
```


Rules for asset extraction:

1. **Resolve actual URLs**: Extract full source URLs from `<img src>`, `srcset` (prefer highest resolution), `<picture><source>`, `<video src>`, `<source>`, CSS `background-image: url(...)`, `mask-image: url(...)`, and network requests.
2. **Capture Inline SVGs**: For inline `<svg>` elements (logos, icons, decorative graphics) that have no external file URL, serialize their outer HTML (including `viewBox`, `xmlns`, paths, gradients, and defs) and write them directly as `.svg` files into `./design-system-assets/icons/` or `./design-system-assets/logos/`.
3. **Download External Files**: Use available shell/fetch/file tools (e.g., `curl`, `wget`, or browser network buffer export) to download reachable images, videos, Lottie JSONs, and fonts into their respective folders with clean, descriptive filenames (e.g., `hero-dashboard-mockup.webp`, `icon-arrow-right.svg`).
4. **Respect Restrictions**: If an asset is blocked by CORS, hotlink protection, DRM, or authentication, or if local download tools are restricted in the environment, record the original absolute URL, explain the limitation, and mark its download status as `External URL Only (Blocked/Restricted)` in `DESIGN_SYSTEM.md`. Never fabricate corrupt or empty placeholder files.
5. **Cross-reference in Markdown**: Every downloaded file must be documented in `DESIGN_SYSTEM.md` with its exact local relative path (`./design-system-assets/...`), original source URL, dimensions/format, and exact placement/usage in the UI.

## 7. Design analysis checklist

Analyze and document the following categories. Use `Not observed` or `Unknown` where appropriate rather than filling gaps with assumptions.

### A. Design overview

* Overall visual style and design principles
* Brand personality conveyed by the interface
* Density, hierarchy, whitespace, and visual rhythm
* Light/dark themes, if present
* Distinctive visual motifs (e.g., bento grids, glassmorphism, brutalist borders, grain overlays, glowing orbs)

### B. Color system

* Brand/primary and secondary colors
* Accent colors
* Page, section, and surface backgrounds
* Primary, secondary, muted, inverse, and link text colors
* Border/divider colors
* Success, warning, error, and informational colors
* Gradients (linear, radial, conic, mesh), overlays, transparency, and color modes
* Actual usage context and evidence status for every token

### C. Typography

* Font families and fallback stacks
* Font weights and available font files (linked to `./design-system-assets/fonts/` if downloaded)
* Heading hierarchy (H1–H6)
* Body, lead, label, caption, metadata, and button styles
* Font size (including fluid `clamp()` formulas), line-height, letter-spacing, text-transform
* Responsive typography changes
* Arabic/RTL and multilingual behavior, if present

### D. Layout, grid, and layering

* Main container `max-width` and side gutters
* Header, navigation, footer dimensions
* Grid columns, gaps, alignment, and content widths
* Section spacing and vertical rhythm
* Flex/grid patterns and bento-grid structures
* Fluid sizing & math functions (`clamp()`, `min()`, `max()`, `vw`/`vh`/`dvh`)
* Z-index & layering system (stacking context scale across navigation, sticky headers, dropdowns, modals, overlays, and background canvas/media)
* Sidebar and dashboard layouts
* Sticky/fixed positioning
* RTL/LTR layout behavior

### E. Spacing scale

* Repeated `padding`, `margin`, and `gap` values
* Component internal spacing
* Section-to-section vertical spacing across desktop, tablet, and mobile
* Any inferred spacing scale, clearly marked as inferred

### F. Shape, borders, elevation, and visual effects

* Border widths, styles, and subtle inner/gradient borders
* Corner radii by component
* Shadows/elevation levels (`box-shadow` and `drop-shadow`)
* Dividers
* Advanced visual effects: `backdrop-filter` (blur/saturate), `mix-blend-mode`, `clip-path`, CSS `mask-image`, and noise/grain textures

### G. Components and variants

Document each reusable component with:

* Name and purpose
* Anatomy/structure (DOM hierarchy)
* Variants and size options
* Colors and typography
* Padding, gap, radius, border, and shadow
* Responsive behavior
* States: default, hover, focus-visible, active, disabled, loading, error, selected, expanded
* Associated media or icon assets (referencing local paths in `./design-system-assets/`)
* Accessibility/semantic observations
* Routes where it appears

Include, when present:

* Buttons and icon buttons
* Links
* Inputs, selects, checkboxes, radios, textareas
* Search
* Cards, bento cards, and list items
* Navigation, breadcrumbs, tabs, pagination
* Dropdowns, popovers, tooltips
* Modals/dialogs/drawers
* Alerts, badges, tags, toasts
* Tables
* Accordions
* Forms and validation
* Hero, feature, testimonial, pricing, FAQ, CTA, and footer sections
* Empty, loading, and error states

### H. Media assets, iconography, and placement architecture

* **Media Inventory & Formats**:
* Identify all media types used: raster images (`AVIF`, `WebP`, `PNG`, `JPG`), vector graphics (`Inline SVG` vs external `.svg`), videos (`MP4`, `WebM`), interactive animations (`Lottie`, `Rive`), and 3D/Canvas scenes (`WebGL`, `Three.js`, `Spline`).
* Map every extracted asset to its saved local path inside `./design-system-assets/`.


* **Placement, Sizing, and Framing (How media is positioned in the design)**:
* **Container mechanics**: Exact `aspect-ratio`, dimensions, `overflow` clipping, padding, and border radii of media wrappers.
* **Object fitting**: Computed `object-fit` (`cover`, `contain`, `fill`) and exact `object-position` focal points.
* **Layout integration**: Document whether media sits inside grid columns, breaks out of containers (full-bleed / edge-to-edge), overlaps adjacent sections (negative margins / absolute positioning), floats around text, or acts as a background layer (`z-index`).
* **Device & UI framing**: Note if product screenshots/videos are wrapped in browser chrome mockups, phone frames, glowing borders, 3D perspective tilts (`transform: perspective(...) rotateX(...) rotateY(...)`), or multi-layered floating UI cards.


* **Visual Treatments, Masks, and Overlays**:
* **Overlays & Readability**: Document gradient scrims, dark overlays (`::before`/`::after` layers), or backdrop blurs placed over media to keep foreground text legible.
* **Filters & Blending**: CSS `filter` (`brightness`, `contrast`, `saturate`, `grayscale`, `blur`, `drop-shadow`), `backdrop-filter`, and `mix-blend-mode`.
* **Masking & Clipping**: Usage of `mask-image` (e.g., radial/linear fade-outs into the page background) and `clip-path` shapes.


* **Video & Interactive Media Behavior**:
* Extract `<video>` attributes (`autoplay`, `loop`, `muted`, `playsinline`, `poster`, `controls`, `preload`).
* Note whether video/Lottie playback is continuous, hover-triggered, click-triggered, or scrubbed via page scroll.


* **Responsive Media & Loading Strategy**:
* Inspect `<picture>`, `srcset`, and `sizes` to see if mobile receives different crops, aspect ratios, or resolutions than desktop.
* Record `loading="lazy"` vs `eager`, `fetchpriority="high"`, and placeholder strategies (dominant color, blur-up/LQIP, or skeleton shimmer).


* **Iconography & SVGs**:
* Identify the icon library (e.g., Lucide, Heroicons, Phosphor, Feather, custom) if recognizable from class names, attributes, viewBox, or paths.
* Document stroke width, cap/join styles (`stroke-linecap`, `stroke-linejoin`), outline vs solid fills, standard sizes (`16px`, `20px`, `24px`), color inheritance (`currentColor`), and SVG internal gradients/filters.



### I. Responsive behavior

* Tested viewport sizes
* Navigation transformations (e.g., desktop bar to mobile drawer/hamburger)
* Grid/column collapses and reordering (`order`)
* Media resizing, aspect-ratio shifts, or hidden decorative assets on mobile
* Typography and spacing changes
* Overflow and horizontal scrolling patterns (e.g., touch carousels, `scroll-snap-type`)
* Responsive exceptions and untested widths

### J. Interaction, motion, and animation mechanics (How animations work)

* **Animation Engine & Libraries**: Identified JS/CSS libraries driving motion on the site (e.g., GSAP, Framer Motion, Lenis, Lottie, CSS-only).
* **Micro-interactions**: Exact property changes, durations, and `cubic-bezier` curves on hover, focus, and active states (e.g., button lift via `transform: translateY(-2px)`, icon slide `translateX(4px)`, border glow opacity transition).
* **Keyframe & Entrance Animations**: Full breakdown of `@keyframes` rules, initial hidden states (e.g., `opacity: 0; transform: translateY(24px) scale(0.96); filter: blur(4px)`), final revealed states, durations, easings, and stagger delays between sibling elements.
* **Scroll-Driven Effects**: Sticky/pinned sections (`position: sticky`), parallax speed ratios, scroll-linked opacity/scale transforms, smooth-scroll wrappers, and `IntersectionObserver` thresholds.
* **Complex Media & Shaders**: Canvas/WebGL interactive backgrounds, mouse-follow spotlights/gradients, SVG path drawing (`stroke-dasharray` / `stroke-dashoffset`), or infinite marquee loops.
* **Step-by-Step Reproduction Guide**: For every signature animation on the site, provide a concrete "How It Works & How to Rebuild It" recipe with the exact CSS/JS logic required to recreate the effect.
* **Reduced-motion behavior**: Check `@media (prefers-reduced-motion: reduce)` handling.

### K. Accessibility and semantics

* Heading order and landmarks
* Accessible names/labels for controls
* Keyboard focus visibility and navigation
* Contrast concerns (flag concerns; do not claim formal WCAG compliance without testing)
* Alt text and decorative `aria-hidden="true"` usage on media/SVGs
* Reduced-motion support
* RTL and language attributes (`dir="rtl"`, `lang`)

### L. Page templates and composition

* Route inventory
* Shared shell and template types
* Section order for representative pages
* Reusable composition patterns
* Page-specific exceptions

## 8. Generate the deliverable

Ensure all extracted assets are saved in `./design-system-assets/` (or the user-specified folder) and create `DESIGN_SYSTEM.md` in the project root/workspace.

Use this exact structure in `DESIGN_SYSTEM.md`:

1. **Title and Metadata**
* Source URL
* Inspection date
* Local assets directory path (`./design-system-assets/`)
* Scope and inspected routes
* Tested viewport sizes
* Tooling/capability limitations


2. **Executive Summary**
3. **Design Principles and Visual Language**
4. **Design Tokens**
* Color (backgrounds, text, borders, accents, gradients, overlays)
* Typography (families, scale, fluid `clamp` formulas, weights, line-heights)
* Spacing & Fluid Sizing
* Layout, Grid & Z-Index Scale
* Radius & Shape
* Border & Dividers
* Shadow, Elevation & Visual Effects (`backdrop-filter`, `mix-blend-mode`, masks)
* Motion Tokens (durations, `cubic-bezier` easings, stagger steps)
* Breakpoints


5. **Component Specifications**
6. **Navigation and Global Shell**
7. **Page Templates and Route Map**
8. **Media Assets, Placement Architecture & Local Asset Manifest**
* Local folder structure overview
* Complete Asset Manifest Table (mapping every downloaded logo, icon, image, video, Lottie, and font to its local path, format, dimensions, parent component, and CSS placement rules)
* Media placement patterns (wrappers, `object-fit`, aspect ratios, overlays, masks, 3D framing, responsive switching, and loading attributes)


9. **Interaction, Motion & Animation Mechanics (How It Works)**
* Detected animation engines/libraries
* Extracted `@keyframes` and transition code blocks
* Detailed "How It Works & How to Rebuild" breakdown for every signature animation, scroll effect, and micro-interaction


10. **Responsive Behavior**
11. **Accessibility Observations**
12. **Implementation Guidance**
13. **Evidence Ledger and Confidence**
14. **Unknowns, Limitations, and Follow-Up Inspection**

Use concise tables for tokens, asset manifests, and component variants. Use fenced code blocks for CSS custom properties, `@keyframes`, animation recipes, and, when useful, Tailwind theme mappings. Keep the Markdown readable and immediately actionable for a developer.

### Token format

For each token include:

* Token name
* Value
* Usage
* Evidence status (`Verified`, `Observed`, `Estimated`, `Unknown`)
* Source route or element

Example:

| Token | Value | Usage | Evidence |
| --- | --- | --- | --- |
| `--color-brand-primary` | `#4F46E5` | Primary CTA background | Verified — computed style on `/` |
| `--ease-out-expo` | `cubic-bezier(0.16, 1, 0.3, 1)` | Hero card entrance & modal reveal | Verified — stylesheet rule on `/` |
| `--radius-card` | `~16px` | Feature cards | Estimated — screenshot |

Never leave placeholder values such as `#...` in the final document. If a value is unavailable, write `Unknown` and explain why.

### Local Asset Manifest format

Document every extracted file in Section 8 using a clear manifest table:

| Asset ID / Name | Type & Format | Local Path in Project | Original Source URL | Dimensions / ViewBox | Where Used & Placement Rules (`object-fit`, wrapper, overlay, animation) | Status |
| --- | --- | --- | --- | --- | --- | --- |
| `brand-logo-dark` | Inline SVG | `./design-system-assets/logos/brand-logo-dark.svg` | Inline DOM on `/` | `viewBox="0 0 120 32"` | Header left; `height: 28px; width: auto; fill: currentColor` | Downloaded |
| `hero-preview` | WebP Image | `./design-system-assets/images/hero-preview.webp` | `https://example.com/hero.webp` | `1920x1080` (`16:9`) | Hero right column; `object-fit: cover; border-radius: 16px`, wrapped in 3D tilt container with radial bottom fade mask | Downloaded |

### Animation Mechanics format

For each notable animation or interaction documented in Section 9, include:

* **Animation Name & Target Element**: What element moves and on which route.
* **Trigger & Engine**: e.g., `CSS Hover`, `IntersectionObserver on scroll into view`, `GSAP ScrollTrigger (scrub: true)`.
* **Initial State -> Final State**: Exact CSS properties before and after (e.g., `opacity: 0; transform: translateY(30px)` -> `opacity: 1; transform: translateY(0)`).
* **Timing & Easing**: Duration, delay, stagger interval, and exact `cubic-bezier` or spring parameters.
* **Reproduction Code Snippet**: Ready-to-use CSS (`@keyframes` / `transition`) or JS snippet showing how to implement it.

### Implementation guidance

Translate observations into reusable implementation advice, without assuming a framework:

* Suggested semantic token naming
* CSS custom properties
* Component variants
* Media wrapper utilities and local asset referencing
* Animation utility classes and scroll-observer setup
* Responsive rules
* Reuse versus page-specific styling
* RTL considerations when applicable

If the user asks for a specific stack, provide a corresponding mapping (for example CSS variables, Tailwind config, React/Framer Motion components, or Flutter theme/animations). Do not add a large implementation codebase unless requested.

## 9. Quality gate before completion

Before returning the response, verify:

* The MCP preflight was completed and its availability is accurately reported.
* The URL and scope are correct.
* Every discovered route is either inspected, grouped under a representative template with rationale, or listed as not inspected with a reason.
* Desktop, tablet, and mobile were tested or missing evidence is disclosed.
* Global shell and all observed page templates are documented.
* Colors, typography, dimensions, spacing, z-index, and breakpoints have evidence labels.
* Media assets (logos, icons/inline SVGs, images, videos, Lotties, fonts) were downloaded into `./design-system-assets/` (or failed downloads are explicitly explained) and mapped in the Asset Manifest table inside `DESIGN_SYSTEM.md`.
* Media placement mechanics (`object-fit`, aspect ratios, masks, overlays, framing) are fully documented.
* Animations and transitions include exact mechanical explanations ("How It Works"), triggers, timings, and reproduction code rather than vague visual descriptions.
* No guessed value is presented as verified.
* Components include variants and states where observed.
* RTL behavior is addressed if the site supports Arabic or RTL.
* Unknowns and limitations are explicit.
* The output is valid Markdown and contains no fabricated assets, citations, or observations.

## 10. Final response

After writing `DESIGN_SYSTEM.md` and saving the media files, report:

* Exact output path of `DESIGN_SYSTEM.md` and the `./design-system-assets/` directory.
* Summary of downloaded media assets by category (number of logos, SVG icons, images, videos, animations, and fonts saved locally).
* Website URL and inspection scope.
* Number of routes discovered, inspected, grouped, and inaccessible (if determinable).
* Viewports tested.
* Whether Chrome DevTools MCP was confirmed available.
* Important limitations and unknowns.
* A short list of the most useful next steps.

If the MCP was unavailable, do not claim completion. Report the missing prerequisite and wait for the user to connect it.

