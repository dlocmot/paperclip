# Holotech (holotech.pe) — Website Redesign Brief

> Full creative + execution brief for the Holotech website redesign, inside the existing repo (`dlocmot/dinamo-web`). This file is imported by `CLAUDE.md` while the redesign is active. Treat the **Process** and **Definition of Done** sections as binding, not optional. (Prefer a one-shot run instead of phases? You can paste this whole file straight into Claude Code.)

---

## 1. Role & mission

Act as a **world-class creative director and lead WebGL / front-end engineer** — the kind a studio sends in when a client has already rejected three templated proposals. Your mission is to produce the **complete, production-ready code** for an avant-garde, award-winning, *story-driven* website for **Holotech**, a Peruvian B2B "disruptive technology" consultancy.

The current site at https://holotech.pe looks like a first-year HTML exercise on an alpha WordPress build. We are throwing the presentation layer away and replacing it with something that could win an Awwwards SOTD — while still converting serious B2B decision-makers (clinics, SMEs, institutions, public-sector buyers in Peru/LATAM).

Push the boundaries of modern front-end, but **every effect must earn its place**: spectacle in service of credibility and conversion, never decoration for its own sake.

---

## 2. Context: what Holotech is

Holotech is an integrated technology partner with **five service lines** (the "stack"). Use these as the narrative spine — but they are a *summary*, not the source of truth: pull the **real** service content, contact data, and any real figures from the current site/repo per the content-preservation rules in §10.

1. **Redes empresariales** — Enterprise networking & infrastructure (MikroTik/RouterOS-grade design, VLAN segmentation, SD-WAN, high-availability).
2. **Ciberseguridad** — Vulnerability assessment & security hardening (OWASP-based methodology, perimeter & endpoint defense, incident response).
3. **Proyectos CTI / I+D+i** — Science-and-technology innovation projects, structured for **CONCYTEC** and Peru's **Ley 30309** R&D tax incentive.
4. **IA aplicada — AGENTICA** — Applied AI and autonomous agents (this sub-brand is named **AGENTICA**; give it its own visual accent within the system).
5. **Pertinencia educativa** — Educational relevance / curriculum consulting for **MINEDU** and **SUNEDU** compliance.

**Audience:** technical and executive buyers in Peru/LATAM who need to trust an engineering partner. The tone is *precise, confident, slightly futuristic* — not playful startup, not stuffy corporate.

**Primary conversion goal:** book a consultation / submit a qualified lead. There is already a CRM with omnichannel lead capture, magic-link auth and Resend email behind this site — **do not rebuild it**; wire the new contact/lead UI into the existing endpoint (see §10).

---

## 3. Technical stack (non-negotiable — this is a real codebase, not a sandbox)

- **Framework:** Astro 6 (SSR/SSG) deployed on **Cloudflare Workers** via the official Cloudflare adapter.
- **Language:** TypeScript, `strict` mode. No `any` unless justified with a comment.
- **Styling:** Tailwind CSS (Astro/Vite integration). Brand tokens defined once (see §5) and consumed as utilities + CSS custom properties.
- **Animation:** **GSAP 3** with **ScrollTrigger** (plus SplitText/Flip/Observer where they help). GSAP is client-only.
- **3D / shaders:** **Three.js / WebGL** for the hero and 1–2 signature moments only. Lazy-loaded, code-split, gated (see §9). Custom GLSL where it elevates the result.
- **Fonts:** **Space Grotesk** (display) + **Inter** (body). **Self-host** via `@fontsource` (performance + privacy + no Google CDN dependency).

### Critical Astro + Cloudflare gotchas you MUST respect
- Astro ships **zero JS by default**. All GSAP/Three logic goes in `<script>` tags or hydrated islands (`client:visible` / `client:idle`). **Never import GSAP or Three in component frontmatter / SSR scope** — it will break the Worker build.
- **Register ScrollTrigger client-side** (`gsap.registerPlugin(ScrollTrigger)` inside the client script).
- If **View Transitions** are enabled, re-initialise GSAP/ScrollTrigger and Three on `astro:page-load` and clean up on `astro:before-swap` (kill triggers, dispose renderer/geometries) to avoid leaks and double-binding.
- The SSR runtime is the **Workers runtime, not Node** — no Node-only APIs in server code. Keep Three.js out of the server bundle entirely (client `import()` only). Keep the Worker lean.
- Use `astro:assets` / `<Image>` for any raster; **inline the logo SVG** (don't `<img>` it) so its paths can be animated.

---

## 4. Hard rule on aesthetic clichés

The brand palette is **dark (`#0B1320`) with bright accents** — which is *exactly* one of the three over-used "AI-generated" looks (near-black background + a single neon accent). The dark base is mandated by the brand and stays. So the **distinctiveness must come from elsewhere**, specifically:

- the **microchip / silicon-die motif** (the logo icon — see §5);
- the **dual-accent system** (teal **and** yellow used with discipline, not one lone neon);
- **Space Grotesk** as a real typographic voice;
- the **signature WebGL hologram** (§6/§8).

**Do not** ship "black page + one glowing color + big centered headline." If your first draft looks like that, it has failed the brief.

---

## 5. Brand system (use verbatim)

### Logo
Two lockups are provided in the repo assets — place/keep them under `src/assets/brand/` (or `public/brand/`):

- `lockup-horizontal-dark.svg` — full horizontal lockup (icon + "HOLOTECH" + "DISRUPTIVE TECHNOLOGY").
- `lockup-vertical-dark.svg` — full vertical lockup (footer, mobile splash).

**The icon** is a stylised **microchip / silicon die**: a teal (`#3AD1C4`) rounded square with dark "pins/contacts" on all four edges and a dark (`#0B1320`) rounded core in the center. **Derive two extra marks from it:**
- an **icon-only mark** (chip without wordmark) for the nav bar and favicon;
- **light-background variants** of both lockups (swap the dark plate for transparent/off-white, keep teal + dark-navy wordmark, or recolor the wordmark for contrast).

Usage: icon-only in the sticky nav; full horizontal lockup in hero/header where space allows; vertical lockup in the footer. The chip icon is the hook for the page-load animation and the hero hologram.

### Color tokens (exact hex)
Define them **once** as CSS custom properties (keep the brand-manual names) and expose them to Tailwind. Example for Tailwind v4 (CSS-first `@theme`); use `theme.extend.colors` in `tailwind.config` if the project is on v3.

```css
/* src/styles/tokens.css */
:root {
  /* Principal */
  --holotech-teal:     #3AD1C4; /* brand · chip · accents */
  --holotech-dark:     #0B1320; /* corporate dark background · dark text */
  --holotech-yellow:   #F5C518; /* wordmark · secondary CTA */
  --holotech-offwhite: #F7F7F2; /* alternative light background */
  --holotech-white:    #FFFFFF; /* clean background */

  /* Neutros */
  --gray-100: #F1F5F9; /* soft fills */
  --gray-300: #CBD5E1; /* borders, dividers */
  --gray-500: #64748B; /* secondary text */
  --gray-700: #334155; /* body text on light */
  --gray-900: #0F172A; /* headings */

  /* Semánticos */
  --color-success: #22C55E;
  --color-warning: #F59E0B;
  --color-error:   #EF4444;
  --color-info:    #3B82F6;
}

@theme {
  --color-teal:     var(--holotech-teal);
  --color-dark:     var(--holotech-dark);
  --color-yellow:   var(--holotech-yellow);
  --color-offwhite: var(--holotech-offwhite);
  --color-brand-white: var(--holotech-white);

  --color-gray-100: var(--gray-100);
  --color-gray-300: var(--gray-300);
  --color-gray-500: var(--gray-500);
  --color-gray-700: var(--gray-700);
  --color-gray-900: var(--gray-900);

  --color-success: var(--color-success);
  --color-warning: var(--color-warning);
  --color-error:   var(--color-error);
  --color-info:    var(--color-info);
}
```

**Color discipline (contrast / WCAG AA):**
- Long-form **body text is never teal or yellow.** Body on dark = white / `--holotech-offwhite` / `--gray-100`; body on light = `--gray-700`; headings on light = `--gray-900`.
- **Teal** = primary accent, interactive states, the chip/hologram glow, focus rings, signal pulses.
- **Yellow** = used sparingly for the wordmark and **secondary CTAs / highlights** only — it's the spark, not the wallpaper.
- Reserve semantic colors strictly for form states and system feedback.

### Typography
- **Display / headings:** Space Grotesk (weights 500/700). Tight tracking on large sizes; make the type a *feature* (oversized hero, confident scale jumps), not a neutral delivery vehicle.
- **Body / UI:** Inter (400/500/600).
- Self-host with `@fontsource`, `font-display: swap`, and `<link rel="preload">` for the hero display weight to avoid CLS.
- Define a clear modular type scale and use it consistently.

---

## 6. Creative direction & the signature element

**Concept: "From silicon to strategy" — a signal traveling through a chip.** Holotech turns raw technology (the silicon die) into business outcomes. The whole site behaves like the inside of a precision instrument: a dark computational void, faint volumetric grid, teal circuit traces carrying light, the yellow spark of decision.

**The one signature element (spend your boldness here):** a real-time **WebGL "holographic silicon die"** in the hero — a 3D interpretation of the chip icon:
- iridescent **fresnel / rim glow in teal**, subtle holographic scanlines and flicker (it's *Holo*tech);
- gentle **parallax to the pointer**, ambient idle rotation;
- **scroll-driven transformation**: as the user scrolls into the service stack, the die rotates/“opens,” and **teal signal pulses run outward along circuit traces** toward each service.
- tasteful bloom post-processing; keep it elegant, not arcade.

Everything *around* this hero stays quiet and disciplined. Restraint is the strategy: one unforgettable centerpiece, surrounded by precise typography, generous space, and crisp structural devices.

---

## 7. Narrative & page architecture (single-page primary, with deep sections)

Write **Spanish (es-PE) UI copy** (audience is Peruvian/LATAM); keep brand taglines as-is ("DISRUPTIVE TECHNOLOGY"). Copy is design material — active voice, plain verbs, sentence case, no filler. **Ground every line in the real current content (see §10):** polish and sharpen the wording, but never introduce facts, numbers, or claims that aren't already true of Holotech.

1. **Hero (the thesis).** The holographic die + one sharp value proposition (e.g. *"Ingeniería tecnológica para decisiones de alto impacto."*). Primary CTA: *Agenda una consultoría.* Secondary (yellow): *Explora la plataforma.* Minimal, confident, no stat-soup.
2. **Manifiesto / posicionamiento.** Short, opinionated statement of who Holotech is — the silicon-to-strategy idea, made concrete.
3. **El stack (las 5 capacidades).** Present the five service lines as **layers of the chip / an integrated stack**, which encodes the real truth (one integrated partner, not five vendors). Each capability gets: a focused headline, 1–2 lines of value, a **real** proof point *where one exists* (don't invent one), and a relevant micro-visual that ties back to the chip/trace motif. Give **AGENTICA** its own accent treatment.
   - *Note on numbering:* only use `01–05` markers if you commit to the "stack/layers" reading; otherwise skip arbitrary numbers — they shouldn't be decoration.
4. **Pruebas / credibilidad.** Methodology (OWASP-based security, R&D framework alignment), **real** certifications/frameworks, and anonymized case snippets. Animated counters **only with real numbers sourced from the client / current site** — if a figure isn't available, omit the counter or mark it `TODO(stakeholder)`; never fabricate. This is where B2B trust is won, so accuracy beats impressiveness.
5. **Cómo trabajamos.** A crisp process section (diagnose → design → implement → operate), expressed in the circuit/signal language.
6. **CTA / contacto.** The conversion moment: a refined lead form **posting to the existing CRM endpoint** (see §10), plus the omnichannel options (WhatsApp/email/phone). Make success and error states explicit and on-brand.
7. **Footer.** Vertical lockup, contact, social, and a Peru-appropriate **privacy / data-protection note** (Ley 29733 / habeas data) and cookie handling.

---

## 8. Signature interactions (GSAP)

Orchestrate, don't scatter:

- **Page-load sequence:** the chip icon **assembles from its pin rectangles** (the SVG `<rect>` pins fly/scale into place), then the wordmark reveals via SplitText / a brief text-scramble. One choreographed moment, ~1.2s, skippable for reduced motion.
- **Pinned service stack:** ScrollTrigger pins the chip while each capability "lights up" a region/trace; **teal pulses travel along SVG paths** (stroke-dashoffset / draw effect) syncing the hero die with the scrolled section.
- **Micro-interactions:** magnetic CTA buttons, `focus-visible` teal rings, hover state changes on cards, optional custom cursor (off under reduced motion).
- **Reveals:** scroll-triggered, staggered, restrained — never on every element.
- (Optional) a **tech-stack / clients marquee** and a horizontal "circuit map" of the five services on desktop.

---

## 9. Quality floor (build to it without announcing it)

**Accessibility (WCAG 2.2 AA):** semantic landmarks, logical heading order, skip link, full keyboard nav, visible focus, ARIA only where needed, AA contrast everywhere (apply §5 color discipline). 

**Reduced motion / low power:** honor `prefers-reduced-motion` — disable scroll-jacking, parallax, autoplay, and the WebGL hero, replacing it with a **pre-rendered static poster** (or an elegant CSS/SVG chip with a soft gradient sheen). Also gate the Three.js scene behind `prefers-reduced-data`, `navigator.connection?.saveData`, device-capability checks, and an `IntersectionObserver` (only init when in viewport). Provide a graceful no-WebGL fallback.

**Performance / Core Web Vitals:** LCP < 2.5s, CLS < 0.1, INP < 200ms; **Lighthouse ≥ 90** across Performance/Accessibility/Best-Practices/SEO. Dynamic `import()` + code-split Three.js so it never blocks LCP; preload hero font; optimize/serve images via `astro:assets`; no layout shift from fonts or the canvas.

**Responsive:** mobile-first; the layout must be beautiful at 360px. On small/low-end devices, scale down or disable the WebGL hero and lean on the static treatment.

**SEO / i18n:** per-page `<title>`/meta, Open Graph + Twitter cards, **JSON-LD** (`Organization` + `Service`), canonical, sitemap, robots. Default locale **es-PE**; structure routing so an English locale can be added later (hreflang if/when added). Don't over-scope i18n now — just don't block it architecturally.

---

## 10. Engineering standards & repo integration

**This is a redesign of an existing Astro + Cloudflare Workers site (`dlocmot/dinamo-web`), not a greenfield build. First, inspect before you change anything:**
- map `src/pages`, `src/layouts`, `src/components`, `src/styles`, the Cloudflare adapter + `wrangler`/`astro.config` setup, env bindings (**D1 / R2 / Resend**), and the existing **lead-capture / CRM** integration and its endpoint(s).
- **Preserve all backend integrations.** The new contact/lead form must keep posting to the **existing endpoint**; magic-link auth, CRM, and bindings stay untouched and functional. Do not break SSR or the Worker deploy.

**Content & information preservation (do NOT invent facts):**
- Before writing any copy, **inventory the real content of the current site** from the repo source (and cross-check the live https://holotech.pe): service descriptions, **contact details (phone, WhatsApp, email, address)**, company/legal identity (razón social, RUC), real metrics / case studies / client references, existing legal & privacy text, current pages, and existing SEO metadata.
- **Migrate every factual element faithfully** — contact data, legal/privacy text, and any real numbers are carried over verbatim, never altered or silently dropped.
- Marketing prose **may be rewritten and elevated** for quality, but it must stay grounded in the real offering. **Never fabricate** metrics, statistics, certifications, client names, awards, or claims.
- Where a stat or proof point would strengthen the design but **no real value exists, insert an explicit `TODO(stakeholder)` placeholder** — do not invent a number to fill a counter or a testimonial to fill a slot.
- **Preserve existing routes/URLs** (or add redirects) so inbound links and SEO don't break; carry over or improve existing meta content rather than discarding it.
- If something in the current site is ambiguous or looks wrong, **flag it for the stakeholder** instead of guessing.

**Then build the new presentation layer progressively:**
- Components in `src/components/` (sections under `src/components/sections/`), shared shell in `src/layouts/`, tokens/global CSS in `src/styles/`, brand assets under `src/assets/brand/`.
- Interactivity as islands (`client:visible` / `client:idle`); GSAP and Three imported **client-side only**; ScrollTrigger registered client-side; cleanup wired to `astro:page-load` / `astro:before-swap` if View Transitions are on.
- Keep CSS specificity clean (watch section vs. element selectors fighting over padding/margins).
- Add new deps explicitly to `package.json` (`gsap`, `three`, `@fontsource/space-grotesk`, `@fontsource/inter`) and note any postprocessing addons.
- Commit in logical, reviewable steps.

---

## 11. Process (do this before writing code, then critique)

1. **Plan a compact token/design system in your reasoning:** palette is given (§5); choose the **single signature element** (the holographic die — confirm or improve it); define the **type scale**; sketch the **layout concept with ASCII wireframes** for hero + service stack + contact.
2. **Self-critique against the cliché rule (§4).** If any part reads like the generic "black + one neon accent + centered headline" default, revise it and state what you changed and why. The chip motif, dual teal/yellow, Space Grotesk, and the hologram are where uniqueness lives.
3. **Build** to the revised plan, deriving every color/type decision from the tokens.
4. **Critique again** — take screenshots if your environment supports it; verify responsive (360px up), keyboard focus, reduced-motion fallback, and Lighthouse before calling it done. Apply "remove one accessory": cut any effect that doesn't serve the brief.

---

## 12. Definition of Done

- [ ] Existing repo inspected; SSR, Cloudflare bindings, and CRM/lead endpoint **preserved and working**.
- [ ] New contact form posts to the **existing** lead endpoint with on-brand success/error states.
- [ ] Real site content **inventoried and migrated**: service descriptions, contact data, legal/privacy text, and existing SEO preserved; existing routes kept or redirected.
- [ ] **Zero fabricated facts** — no invented metrics, counters, certifications, clients, awards, or claims; any missing figure marked `TODO(stakeholder)`.
- [ ] Brand tokens (§5) defined once, consumed via Tailwind + CSS vars; color discipline respected (no teal/yellow body text; AA contrast).
- [ ] Logos placed; icon-only + light variants derived; logo SVG inlined for animation; favicon set.
- [ ] Space Grotesk + Inter self-hosted, preloaded, no CLS.
- [ ] WebGL holographic-die hero: pointer parallax + scroll transformation + signal pulses, with bloom — **and** a static poster fallback.
- [ ] GSAP page-load choreography + pinned service stack + restrained reveals; all skipped under reduced motion.
- [ ] Three.js code-split, viewport/capability/save-data/reduced-motion gated; never blocks LCP; cleaned up on navigation.
- [ ] Five service lines presented as an integrated "stack," AGENTICA distinctly accented; Spanish (es-PE) copy that reads like a copywriter wrote it.
- [ ] WCAG 2.2 AA: semantics, skip link, keyboard nav, visible focus, ARIA where needed.
- [ ] Responsive and beautiful from 360px; **Lighthouse ≥ 90** all categories; LCP < 2.5s / CLS < 0.1 / INP < 200ms.
- [ ] SEO: meta/OG/Twitter, JSON-LD (Organization + Service), sitemap, canonical; es-PE default with i18n-ready routing.
- [ ] No `any` without justification; TypeScript strict passes; Astro build + Cloudflare deploy succeed.

**Output:** the complete, production-ready Astro components, global styles/tokens, Tailwind theme, GSAP and Three.js modules with fallbacks, and any config/dependency changes — integrated into the existing project and ready to `git commit` and deploy to Cloudflare Workers.
