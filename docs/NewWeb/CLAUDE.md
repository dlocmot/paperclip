# CLAUDE.md — Holotech (dinamo-web)

Persistent project rules for Claude Code. Keep this file short. The full creative + execution brief for the website redesign lives in `docs/redesign-brief.md` and is imported below while the redesign is active.

@docs/redesign-brief.md

## Stack
- Astro 6 (SSR/SSG) on **Cloudflare Workers** via the official Cloudflare adapter.
- TypeScript, `strict`. No `any` without a justifying comment.
- Tailwind CSS. Brand tokens live in `src/styles/tokens.css` (CSS custom properties + Tailwind theme).
- GSAP 3 + ScrollTrigger for animation (client-only). Three.js / WebGL for the hero + 1–2 signature moments (client-only, lazy-loaded).
- Fonts self-hosted via `@fontsource` (Space Grotesk display, Inter body).

## Commands (verify against package.json before relying on them — run `/init` if unsure)
- Dev: `npm run dev`
- Build: `npm run build`
- Preview on the Workers runtime: `npm run preview` (Astro + `wrangler dev`)
- Deploy: `npm run deploy` (or `wrangler deploy`)
- Typecheck: `npm run astro check` / `tsc --noEmit`
- **Run build + typecheck and confirm they pass before proposing any commit.**

## IMPORTANT — safety boundaries (do not violate)
- **Never break the backend.** The CRM, lead-capture endpoint(s), magic-link auth, and the D1 / R2 / Resend bindings stay functional. The contact form must keep POSTing to the existing endpoint. Do not change SSR or the Worker deploy config without saying so explicitly.
- **Client vs server placement.** Do NOT import GSAP or Three.js in component frontmatter / SSR scope — it breaks the Worker build. They are client-only (`<script>` or `client:*` islands); register ScrollTrigger client-side. The SSR runtime is the Workers runtime, NOT Node — no Node-only APIs in server code.
- **Never fabricate facts.** Do not invent metrics, counters, certifications, client names, awards, prices, or claims. Migrate real content (services, contact data, legal/privacy text, real numbers) faithfully from the current site/repo. If a figure is missing, write `TODO(stakeholder)` — never a made-up number.
- **Never commit secrets** or `.env` / `.dev.vars` files. No tokens or credentials in source or in this file.
- **Preserve routes/URLs** (or add redirects) so existing links and SEO don't break.

## Human approval required before
- Changing routing, the Cloudflare adapter, `wrangler` / `astro.config`, or env bindings.
- Adding or upgrading dependencies (list them and the reason first).
- Any D1 schema change or destructive operation.
- Editing files under `src/**/api/` or anything wired to the CRM.

## Conventions
- Components in `src/components/` (page sections under `src/components/sections/`), shell in `src/layouts/`, global CSS in `src/styles/`, brand assets in `src/assets/brand/`.
- Inline the logo SVG (don't `<img>` it) so its paths can be animated.
- UI copy in Spanish (es-PE); keep brand taglines as-is ("DISRUPTIVE TECHNOLOGY").
- Respect `prefers-reduced-motion` everywhere; provide a static fallback for the WebGL hero. Gate Three.js behind viewport + device-capability + save-data checks so it never blocks LCP.
- WCAG 2.2 AA: semantic landmarks, visible keyboard focus, AA contrast. Body text is never teal or yellow.
- Conventional commits (`feat:`, `fix:`, `refactor:`). Commit in small, reviewable steps.

## Workflow
Build the redesign in verifiable phases (recon → foundations → static design → GSAP → WebGL hero → conversion → hardening). Finish and verify each phase (build + screenshots) before starting the next. Full detail and the acceptance checklist are in `docs/redesign-brief.md` (Process + Definition of Done).

## When the redesign is finished
Remove the `@docs/redesign-brief.md` import line near the top so the long brief stops loading on every future session. Keep the rest of this file as the repo's standing contract.
