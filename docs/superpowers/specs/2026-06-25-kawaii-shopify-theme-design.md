# Only Cute Stuff — Kawaii Shopify Theme (Design Spec)

**Date:** 2026-06-25
**Store:** only-cute-stuff-2.myshopify.com (live) · dev-store-3kajxn4y.myshopify.com (dev)
**Reference (visual only):** https://kawaii-charm-theme.lovable.app/

## Goal

Build a custom kawaii/pastel storefront for **Only Cute Stuff** as a proper Shopify
Online Store 2.0 theme, recreating the *vibe* of the reference Lovable site (not a
pixel clone, not a code port — the reference is a React SPA and is used purely as a
visual reference). The theme is built by restyling **Dawn** and adding custom sections.

## Decisions (locked during brainstorming)

- **Fidelity:** Same vibe, adapted to the real catalog + own copy.
- **Base:** Restyle **Dawn** (Shopify official OS 2.0 reference theme). Keep Dawn's
  working cart drawer, variant pickers, search, and checkout integration; do not
  reimplement them.
- **v1 sections:** Hero + announcement bar, Collections grid, Trending products +
  Testimonials. (Brand story / FAQ / Instagram deferred.)
- **Content during build:** Generated **sample products/collections on the dev store**;
  real products added to the live store later. Nothing hard-coded in production.
- **Branding:** Palette + fonts derived from the reference, set as editable Dawn theme
  settings. Text logo "Only Cute Stuff" for v1.

## Architecture & workflow

- **Repo:** new git repo `only-cute-stuff-theme` on the droplet, seeded from a fresh
  Dawn clone (`shopify theme init` / clone of Shopify/dawn).
- **Dev loop:** `shopify theme dev --store dev-store-3kajxn4y` for live preview →
  `shopify theme push` (unpublished) to the dev store for review.
- **Go-live:** publish the reviewed theme to `only-cute-stuff-2` (push as unpublished,
  then publish from the admin or `shopify theme push --live` after approval).
- **Editability:** all content via theme-editor sections/blocks/settings; no hard-coded
  product data in production templates.

## Visual system (editable Dawn settings)

- **Palette** (tweakable in editor): blush `#FFD6E8`, cream `#FFF8F3`, soft lilac
  `#E7D6FF`, mint accent `#D6F5E3`, warm charcoal text `#4A3F47`, hot-pink CTA `#FF7FB0`.
  Implemented as Dawn `color_schemes`.
- **Type:** rounded display font for headings (Fredoka / Quicksand from Shopify's font
  library) + soft humanist body (Nunito/Quicksand), via Dawn `type_*` settings.
- **Motifs:** large rounded corners, soft pastel shadows, decorative glyphs (✿ ♡ ✦) as
  heading accents/dividers. Delivered via a small custom CSS layer + section settings.

## Components (units, each independently editable)

| Unit | Type | Source | Responsibility |
|---|---|---|---|
| Announcement bar | section | restyle Dawn `announcement-bar` | Rotating promo messages |
| Header | section | restyle Dawn `header` | Logo, nav, search, cart drawer |
| `cute-hero` | **new** section | custom | Tagline, subtitle, 2 CTAs, ★ rating badge, image |
| `cute-collection-cards` | **new** section | custom | Image cards linking to real collections |
| Trending products | section | restyle Dawn `featured-collection` | "What besties are loving" row |
| `cute-testimonials` | **new** section | custom | Review cards: stars + verified badge |
| Footer | section | restyle Dawn `footer` | Multi-column links + newsletter |
| Product / Collection / Cart / Search / 404 | templates | restyle Dawn | Core shopping flows |

Each new section exposes schema settings (text, images, links, color scheme, block
lists) so it is editable and reusable, and is understandable without reading Dawn's
internals.

## Data flow

Custom sections render live Shopify objects: `cute-collection-cards` references
`collection` objects chosen in the editor; Trending points at a selected collection;
product/variant/cart run through Dawn's existing Liquid + AJAX cart. The theme reads
data from Shopify; it never stores or fakes it in production.

## Testing / verification

1. `shopify theme check` — Liquid/theme linting passes.
2. `shopify theme dev` — visual review of each section against the reference vibe.
3. Manual click-through on the dev store: home → collection → product → add to cart →
   cart drawer → checkout entry; search; 404.
4. Quick Lighthouse/perf + mobile-responsive sanity pass.
5. Publish to live only after explicit approval.

## Out of scope (v1)

- Rewards / "Cute Club" loyalty functionality (requires a loyalty app, e.g. Smile.io).
- Live Instagram feed (static images only if added later).
- Brand-story + FAQ sections (deferred).
- Real product/collection content on the live store (added by the owner later).

## Risks / notes

- Theme work uses the **Shopify Theme CLI** (`shopify theme …`), separate from the
  `shopify app …` flow used for the Hermes API integration — different auth/commands.
- Dev store sample data is for preview only; real catalog must exist before go-live for
  the live store to look complete.
- Font availability is limited to Shopify's font library unless custom fonts are added.
