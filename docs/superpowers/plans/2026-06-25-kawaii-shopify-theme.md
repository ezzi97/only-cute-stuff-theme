# Only Cute Stuff — Kawaii Shopify Theme Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a kawaii/pastel Online Store 2.0 theme for Only Cute Stuff by restyling Dawn and adding three custom sections, previewed on a dev store and then published live.

**Architecture:** Clone Dawn into `/root/only-cute-stuff-theme` on the droplet. Restyle via theme settings (color schemes, fonts) + one global CSS asset. Add three new custom sections (`cute-hero`, `cute-collection-cards`, `cute-testimonials`) and assemble the homepage via `templates/index.json`. Preview with `shopify theme dev` against `dev-store-3kajxn4y`, then push/publish to `only-cute-stuff-2`.

**Tech Stack:** Shopify Dawn (OS 2.0), Liquid, Shopify Theme CLI (`shopify theme …`), Shopify font library, plain CSS.

## Global Constraints

- **NO git on the laptop.** Never run git commands in the local laptop session. On the droplet git is allowed, but this plan checkpoints with file saves + `shopify theme push` rather than commits. (Verbatim user rule.)
- **Theme CLI only:** use `shopify theme …`, NOT `shopify app …` (that flow is for the Hermes API app).
- **Dev store for build:** `dev-store-3kajxn4y.myshopify.com`. **Live store:** `only-cute-stuff-2.myshopify.com`. Never push `--live` without explicit user approval.
- **No hard-coded product data in production templates** — sections read live Shopify objects and expose editor settings.
- **Restyle, don't rewrite:** keep Dawn's cart drawer, variant pickers, search, checkout. Only override styling + add new sections.
- **Palette (color_schemes):** blush `#FFD6E8`, cream `#FFF8F3`, soft lilac `#E7D6FF`, mint `#D6F5E3`, warm charcoal text `#4A3F47`, hot-pink CTA `#FF7FB0`.
- **Fonts:** heading = Fredoka (`fredoka_n4` family handle), body = Nunito (`nunito_n4`). Both from Shopify font library.
- **Verification per task:** `shopify theme check` passes (no new errors) + visual confirm in `shopify theme dev` preview.

---

### Task 1: Project setup — Dawn baseline on the dev store

**Files:**
- Create: entire Dawn theme tree under `/root/only-cute-stuff-theme/` (clone).

**Interfaces:**
- Produces: a working Dawn checkout connected to `dev-store-3kajxn4y`, plus `shopify theme dev` running.

- [ ] **Step 1: Confirm Theme CLI is available**

Run: `shopify version`
Expected: prints a CLI version (e.g. `3.x`). If missing: `npm i -g @shopify/cli @shopify/theme`.

- [ ] **Step 2: Clone Dawn into the project dir (no git)**

```bash
cd /root/only-cute-stuff-theme
# Download Dawn source WITHOUT git history
curl -L -o /tmp/dawn.tar.gz https://github.com/Shopify/dawn/archive/refs/heads/main.tar.gz
tar -xzf /tmp/dawn.tar.gz -C /tmp
cp -r /tmp/dawn-main/. /root/only-cute-stuff-theme/
rm -rf /root/only-cute-stuff-theme/.github /tmp/dawn-main /tmp/dawn.tar.gz
```
Expected: `sections/`, `templates/`, `config/`, `assets/`, `layout/theme.liquid` now exist.

- [ ] **Step 3: Lint baseline**

Run: `cd /root/only-cute-stuff-theme && shopify theme check`
Expected: completes; note the baseline offense count (Dawn ships clean or near-clean).

- [ ] **Step 4: Start dev preview against the dev store**

Run: `shopify theme dev --store dev-store-3kajxn4y.myshopify.com`
Expected: prints a local preview URL (and a *.trycloudflare.com share URL). Open it — Dawn renders. Leave running in a separate terminal for later tasks (or restart per task).

- [ ] **Step 5: Checkpoint**

```bash
shopify theme push --unpublished --store dev-store-3kajxn4y.myshopify.com --theme "Only Cute Stuff (dev)"
```
Expected: an unpublished theme named "Only Cute Stuff (dev)" appears under the dev store's Themes; note its theme ID for reuse.

---

### Task 2: Sample catalog on the dev store

**Files:** none (data lives in the dev store, not the theme).

**Interfaces:**
- Produces: ≥3 collections (`plushie-club`, `pearl-and-bow`, `desk-cuties`) and ≥12 published products with images, so grids render fully.

- [ ] **Step 1: Add sample products**

In the dev store admin (`dev-store-3kajxn4y.myshopify.com/admin`): **Settings → Stores / or the dev-store "Import sample data" option**, OR create manually: Products → Add product ×12, each with a title, price, one image, and Status = Active.
Expected: 12 active products visible under Products.

- [ ] **Step 2: Create the three collections**

Products → Collections → Create collection ×3: handles `plushie-club`, `pearl-and-bow`, `desk-cuties`. Make them **Manual** or **Automated**, add products to each, give each a collection image.
Expected: 3 collections, each non-empty, each with an image.

- [ ] **Step 3: Verify data via preview**

Refresh the `shopify theme dev` URL → `/collections/plushie-club`.
Expected: products show in Dawn's default collection grid.

- [ ] **Step 4: Checkpoint** — no theme files changed; nothing to push.

---

### Task 3: Kawaii color schemes + fonts (global theme settings)

**Files:**
- Modify: `config/settings_data.json` (color_schemes + font handles)

**Interfaces:**
- Produces: `scheme-1` = cream background/charcoal text, `scheme-pink` = blush, `scheme-lilac` = lilac, with hot-pink buttons; heading font Fredoka, body Nunito. Later sections reference these scheme IDs.

- [ ] **Step 1: Edit color schemes**

In `config/settings_data.json`, under `"current" → "color_schemes"`, set (keep Dawn's structure; change values):
```json
"scheme-1": { "settings": {
  "background": "#FFF8F3", "background_gradient": "",
  "text": "#4A3F47", "button": "#FF7FB0", "button_label": "#FFFFFF",
  "secondary_button_label": "#FF7FB0", "shadow": "#FFD6E8" } },
"scheme-2": { "settings": {
  "background": "#FFD6E8", "text": "#4A3F47", "button": "#FF7FB0",
  "button_label": "#FFFFFF", "secondary_button_label": "#4A3F47", "shadow": "#E7D6FF" } },
"scheme-3": { "settings": {
  "background": "#E7D6FF", "text": "#4A3F47", "button": "#FF7FB0",
  "button_label": "#FFFFFF", "secondary_button_label": "#4A3F47", "shadow": "#D6F5E3" } }
```

- [ ] **Step 2: Set fonts**

In the same `"current"` settings object set:
```json
"type_header_font": "fredoka_n4",
"type_body_font": "nunito_n4"
```

- [ ] **Step 3: Lint**

Run: `shopify theme check`
Expected: no new offenses; `settings_data.json` remains valid JSON.

- [ ] **Step 4: Preview**

Refresh preview homepage.
Expected: cream background, charcoal rounded-font headings, pink buttons.

- [ ] **Step 5: Checkpoint**

```bash
shopify theme push --store dev-store-3kajxn4y.myshopify.com --theme "Only Cute Stuff (dev)"
```

---

### Task 4: Global kawaii CSS layer (motifs)

**Files:**
- Create: `assets/custom-kawaii.css`
- Modify: `layout/theme.liquid` (add one `stylesheet_tag` line in `<head>`)

**Interfaces:**
- Produces: global rounded corners, soft pastel shadows, and `.kawaii-glyph` helper used by custom sections.

- [ ] **Step 1: Create the CSS asset**

`assets/custom-kawaii.css`:
```css
:root { --kawaii-radius: 22px; --kawaii-shadow: 0 8px 24px rgba(255,127,176,.18); }
.button, .card, .price, .product-card-wrapper, .collection-card,
.cute-hero__cta, .cute-card { border-radius: var(--kawaii-radius) !important; }
.card, .cute-card { box-shadow: var(--kawaii-shadow); }
.button { letter-spacing:.02em; }
.kawaii-glyph { display:inline-block; margin:0 .35rem; color:#FF7FB0; }
.cute-section-title { text-align:center; font-weight:600; }
.cute-section-title .kawaii-glyph { font-size:.8em; vertical-align:middle; }
```

- [ ] **Step 2: Include it in the layout**

In `layout/theme.liquid`, immediately before `</head>`, add:
```liquid
{{ 'custom-kawaii.css' | asset_url | stylesheet_tag }}
```

- [ ] **Step 3: Lint**

Run: `shopify theme check`
Expected: no new offenses.

- [ ] **Step 4: Preview**

Refresh preview.
Expected: product cards and buttons now have rounded corners + soft pink shadow.

- [ ] **Step 5: Checkpoint**

```bash
shopify theme push --store dev-store-3kajxn4y.myshopify.com --theme "Only Cute Stuff (dev)"
```

---

### Task 5: Announcement bar — rotating promo messages

**Files:**
- Modify: `sections/announcement-bar.liquid` (confirm multi-block rotation; restyle)
- Modify: `templates/index.json` / `config/settings_data.json` header group as needed to add 3 announcement blocks.

**Interfaces:**
- Consumes: `scheme-2` (blush) from Task 3.
- Produces: a top bar cycling 3+ messages.

- [ ] **Step 1: Add blocks in the editor**

In `shopify theme dev` preview → Theme editor → Header → Announcement bar: set color scheme `scheme-2`, add 3 "Announcement" blocks: "✿ Free shipping over $35", "♡ New drops every Friday", "✦ Join Cute Club for points". Enable auto-rotate if present.
Expected: bar rotates the three messages.

- [ ] **Step 2: Lint + preview**

Run: `shopify theme check` → no new offenses. Refresh preview → blush bar rotates.

- [ ] **Step 3: Checkpoint**

```bash
shopify theme push --store dev-store-3kajxn4y.myshopify.com --theme "Only Cute Stuff (dev)"
```

---

### Task 6: Header restyle

**Files:**
- Modify: `config/settings_data.json` (header section settings: logo text, menu, color scheme)

**Interfaces:**
- Produces: text logo "Only Cute Stuff", nav (Shop, Collections, New In, About), sticky header, cart drawer enabled.

- [ ] **Step 1: Configure header in the editor**

Theme editor → Header: set "Only Cute Stuff" as the logo text (or upload later), menu = main menu with Shop/Collections/New In/About (create these links under Navigation pointing to `/collections/all`, `/collections`, a New-In collection, and `/pages/about` placeholder), enable sticky header + cart drawer, color scheme `scheme-1`.
Expected: header shows the name + nav; clicking cart opens the drawer.

- [ ] **Step 2: Lint + preview**

Run: `shopify theme check` → no new offenses. Refresh → header styled, drawer works.

- [ ] **Step 3: Checkpoint**

```bash
shopify theme push --store dev-store-3kajxn4y.myshopify.com --theme "Only Cute Stuff (dev)"
```

---

### Task 7: `cute-hero` section (new) + add to homepage

**Files:**
- Create: `sections/cute-hero.liquid`
- Modify: `templates/index.json` (add `cute-hero` as first content section)

**Interfaces:**
- Produces: section type `cute-hero` with settings `heading, subheading, image, button1_label, button1_link, button2_label, button2_link, rating, review_count, color_scheme`.

- [ ] **Step 1: Create the section**

`sections/cute-hero.liquid`:
```liquid
{%- liquid
  assign scheme = section.settings.color_scheme | default: 'scheme-1'
-%}
<div class="color-{{ scheme }} gradient cute-hero">
  <div class="page-width cute-hero__inner">
    <div class="cute-hero__text">
      <h1 class="cute-hero__heading">
        <span class="kawaii-glyph">✿</span>{{ section.settings.heading }}<span class="kawaii-glyph">✿</span>
      </h1>
      <p class="cute-hero__sub">{{ section.settings.subheading }}</p>
      <div class="cute-hero__buttons">
        {%- if section.settings.button1_label != blank -%}
          <a class="button cute-hero__cta" href="{{ section.settings.button1_link }}">{{ section.settings.button1_label }}</a>
        {%- endif -%}
        {%- if section.settings.button2_label != blank -%}
          <a class="button button--secondary cute-hero__cta" href="{{ section.settings.button2_link }}">{{ section.settings.button2_label }}</a>
        {%- endif -%}
      </div>
      {%- if section.settings.rating != blank -%}
        <p class="cute-hero__rating">★ {{ section.settings.rating }} · {{ section.settings.review_count }}</p>
      {%- endif -%}
    </div>
    {%- if section.settings.image != blank -%}
      <div class="cute-hero__media">
        {{ section.settings.image | image_url: width: 1200 | image_tag: loading: 'eager', class: 'cute-hero__img' }}
      </div>
    {%- endif -%}
  </div>
</div>
<style>
  .cute-hero__inner{display:flex;gap:3rem;align-items:center;padding:5rem 0;flex-wrap:wrap}
  .cute-hero__text{flex:1 1 320px}
  .cute-hero__media{flex:1 1 320px}
  .cute-hero__img{width:100%;border-radius:var(--kawaii-radius);box-shadow:var(--kawaii-shadow)}
  .cute-hero__heading{font-size:3rem;line-height:1.1}
  .cute-hero__buttons{display:flex;gap:1rem;flex-wrap:wrap;margin-top:1.5rem}
  .cute-hero__rating{margin-top:1rem;font-weight:600}
</style>
{% schema %}
{
  "name": "Cute hero",
  "settings": [
    { "type": "text", "id": "heading", "label": "Heading", "default": "Only the cutest things, ever" },
    { "type": "textarea", "id": "subheading", "label": "Subheading", "default": "Hand-picked plushies & accessories, wrapped with love." },
    { "type": "image_picker", "id": "image", "label": "Hero image" },
    { "type": "text", "id": "button1_label", "label": "Button 1 label", "default": "Shop the drop" },
    { "type": "url", "id": "button1_link", "label": "Button 1 link" },
    { "type": "text", "id": "button2_label", "label": "Button 2 label", "default": "Lookbook" },
    { "type": "url", "id": "button2_link", "label": "Button 2 link" },
    { "type": "text", "id": "rating", "label": "Rating", "default": "4.9" },
    { "type": "text", "id": "review_count", "label": "Review count", "default": "12,400+ reviews" },
    { "type": "color_scheme", "id": "color_scheme", "label": "Color scheme", "default": "scheme-1" }
  ],
  "presets": [{ "name": "Cute hero" }]
}
{% endschema %}
```

- [ ] **Step 2: Add to homepage**

In `templates/index.json`, add to `"sections"` and put first in `"order"`:
```json
"cute_hero": { "type": "cute-hero", "settings": { "color_scheme": "scheme-1" } }
```

- [ ] **Step 3: Lint**

Run: `shopify theme check`
Expected: no new offenses; valid schema.

- [ ] **Step 4: Preview + verify**

Refresh homepage; in editor set a hero image + button links (Shop → `/collections/all`).
Expected: hero shows heading with ✿ glyphs, subheading, two pink CTAs, rating, image.

- [ ] **Step 5: Checkpoint**

```bash
shopify theme push --store dev-store-3kajxn4y.myshopify.com --theme "Only Cute Stuff (dev)"
```

---

### Task 8: `cute-collection-cards` section (new) + add to homepage

**Files:**
- Create: `sections/cute-collection-cards.liquid`
- Modify: `templates/index.json`

**Interfaces:**
- Produces: section type `cute-collection-cards` with `collection_card` blocks (`collection`, `custom_title`) and a section `heading`.

- [ ] **Step 1: Create the section**

`sections/cute-collection-cards.liquid`:
```liquid
<div class="color-{{ section.settings.color_scheme | default: 'scheme-1' }} gradient">
  <div class="page-width cute-collections">
    {%- if section.settings.heading != blank -%}
      <h2 class="cute-section-title"><span class="kawaii-glyph">♡</span>{{ section.settings.heading }}<span class="kawaii-glyph">♡</span></h2>
    {%- endif -%}
    <div class="cute-collections__grid">
      {%- for block in section.blocks -%}
        {%- assign coll = block.settings.collection -%}
        {%- if coll != blank -%}
          <a class="cute-card cute-collections__card" href="{{ coll.url }}" {{ block.shopify_attributes }}>
            {%- if coll.featured_image -%}
              {{ coll.featured_image | image_url: width: 700 | image_tag: loading: 'lazy', class: 'cute-collections__img' }}
            {%- endif -%}
            <span class="cute-collections__name">{{ block.settings.custom_title | default: coll.title }}</span>
          </a>
        {%- endif -%}
      {%- endfor -%}
    </div>
  </div>
</div>
<style>
  .cute-collections{padding:4rem 0}
  .cute-collections__grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(220px,1fr));gap:1.5rem;margin-top:2rem}
  .cute-collections__card{display:block;overflow:hidden;text-align:center;text-decoration:none}
  .cute-collections__img{width:100%;aspect-ratio:1;object-fit:cover;display:block}
  .cute-collections__name{display:block;padding:1rem;font-weight:600}
</style>
{% schema %}
{
  "name": "Cute collections",
  "settings": [
    { "type": "text", "id": "heading", "label": "Heading", "default": "Shop by cuteness" },
    { "type": "color_scheme", "id": "color_scheme", "label": "Color scheme", "default": "scheme-1" }
  ],
  "blocks": [
    { "type": "collection_card", "name": "Collection", "settings": [
      { "type": "collection", "id": "collection", "label": "Collection" },
      { "type": "text", "id": "custom_title", "label": "Custom title (optional)" }
    ]}
  ],
  "max_blocks": 6,
  "presets": [{ "name": "Cute collections", "blocks": [
    { "type": "collection_card" }, { "type": "collection_card" }, { "type": "collection_card" }
  ]}]
}
{% endschema %}
```

- [ ] **Step 2: Add to homepage** (after hero)

In `templates/index.json` add to `"sections"` and place after `cute_hero` in `"order"`:
```json
"cute_collections": { "type": "cute-collection-cards",
  "blocks": {
    "b1": { "type": "collection_card", "settings": { "custom_title": "Plushie Club" } },
    "b2": { "type": "collection_card", "settings": { "custom_title": "Pearl & Bow" } },
    "b3": { "type": "collection_card", "settings": { "custom_title": "Desk Cuties" } }
  },
  "block_order": ["b1","b2","b3"], "settings": {} }
```

- [ ] **Step 3: Lint**

Run: `shopify theme check`
Expected: no new offenses.

- [ ] **Step 4: Preview + verify**

In editor, assign each card a real collection (plushie-club, pearl-and-bow, desk-cuties).
Expected: 3 image cards, rounded, linking to their collections.

- [ ] **Step 5: Checkpoint**

```bash
shopify theme push --store dev-store-3kajxn4y.myshopify.com --theme "Only Cute Stuff (dev)"
```

---

### Task 9: Trending products (restyle Dawn `featured-collection`) + add to homepage

**Files:**
- Modify: `templates/index.json` (add a `featured-collection` instance titled "What besties are loving")

**Interfaces:**
- Consumes: Dawn's existing `featured-collection.liquid` (no code change).
- Produces: a product row on the homepage bound to a chosen collection.

- [ ] **Step 1: Add the section to the homepage**

In `templates/index.json` add to `"sections"` and place after `cute_collections`:
```json
"trending": { "type": "featured-collection", "settings": {
  "title": "What besties are loving", "heading_size": "h1",
  "collection": "", "products_to_show": 4, "columns_desktop": 4,
  "color_scheme": "scheme-1", "show_view_all": true } }
```

- [ ] **Step 2: Bind a collection in the editor**

Theme editor → "What besties are loving" → pick the `plushie-club` collection.
Expected: 4 product cards render with prices + add-to-cart.

- [ ] **Step 3: Lint + preview**

Run: `shopify theme check` → no new offenses. Refresh → trending row shows products; "View all" links to the collection.

- [ ] **Step 4: Checkpoint**

```bash
shopify theme push --store dev-store-3kajxn4y.myshopify.com --theme "Only Cute Stuff (dev)"
```

---

### Task 10: `cute-testimonials` section (new) + add to homepage

**Files:**
- Create: `sections/cute-testimonials.liquid`
- Modify: `templates/index.json`

**Interfaces:**
- Produces: section type `cute-testimonials` with `testimonial` blocks (`quote`, `author`, `rating`, `verified`).

- [ ] **Step 1: Create the section**

`sections/cute-testimonials.liquid`:
```liquid
<div class="color-{{ section.settings.color_scheme | default: 'scheme-3' }} gradient">
  <div class="page-width cute-reviews">
    {%- if section.settings.heading != blank -%}
      <h2 class="cute-section-title"><span class="kawaii-glyph">✦</span>{{ section.settings.heading }}<span class="kawaii-glyph">✦</span></h2>
    {%- endif -%}
    <div class="cute-reviews__grid">
      {%- for block in section.blocks -%}
        <div class="cute-card cute-reviews__card" {{ block.shopify_attributes }}>
          <div class="cute-reviews__stars">{% for i in (1..block.settings.rating) %}★{% endfor %}</div>
          <p class="cute-reviews__quote">{{ block.settings.quote }}</p>
          <p class="cute-reviews__author">
            {{ block.settings.author }}
            {%- if block.settings.verified -%}<span class="kawaii-glyph">♡ verified</span>{%- endif -%}
          </p>
        </div>
      {%- endfor -%}
    </div>
  </div>
</div>
<style>
  .cute-reviews{padding:4rem 0}
  .cute-reviews__grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(260px,1fr));gap:1.5rem;margin-top:2rem}
  .cute-reviews__card{padding:1.75rem;background:#fff}
  .cute-reviews__stars{color:#FF7FB0;font-size:1.1rem}
  .cute-reviews__quote{margin:.75rem 0;font-style:italic}
  .cute-reviews__author{font-weight:600}
</style>
{% schema %}
{
  "name": "Cute testimonials",
  "settings": [
    { "type": "text", "id": "heading", "label": "Heading", "default": "Loved by besties" },
    { "type": "color_scheme", "id": "color_scheme", "label": "Color scheme", "default": "scheme-3" }
  ],
  "blocks": [
    { "type": "testimonial", "name": "Testimonial", "settings": [
      { "type": "textarea", "id": "quote", "label": "Quote", "default": "Cutest plushie I've ever owned!" },
      { "type": "text", "id": "author", "label": "Author", "default": "Mochi" },
      { "type": "range", "id": "rating", "label": "Stars", "min": 1, "max": 5, "step": 1, "default": 5 },
      { "type": "checkbox", "id": "verified", "label": "Verified buyer", "default": true }
    ]}
  ],
  "max_blocks": 6,
  "presets": [{ "name": "Cute testimonials", "blocks": [
    { "type": "testimonial" }, { "type": "testimonial" }, { "type": "testimonial" }
  ]}]
}
{% endschema %}
```

- [ ] **Step 2: Add to homepage** (after trending)

In `templates/index.json` add to `"sections"` and place after `trending`:
```json
"testimonials": { "type": "cute-testimonials",
  "blocks": {
    "t1": { "type": "testimonial", "settings": {} },
    "t2": { "type": "testimonial", "settings": { "author": "Bun", "quote": "Packaging is adorable!" } },
    "t3": { "type": "testimonial", "settings": { "author": "Peach", "quote": "Obsessed, ordering again." } }
  },
  "block_order": ["t1","t2","t3"], "settings": {} }
```

- [ ] **Step 3: Lint**

Run: `shopify theme check`
Expected: no new offenses.

- [ ] **Step 4: Preview + verify**

Refresh homepage.
Expected: lilac testimonials section, 3 cards with pink stars + "♡ verified".

- [ ] **Step 5: Checkpoint**

```bash
shopify theme push --store dev-store-3kajxn4y.myshopify.com --theme "Only Cute Stuff (dev)"
```

---

### Task 11: Footer restyle

**Files:**
- Modify: `config/settings_data.json` (footer section settings)

**Interfaces:**
- Produces: multi-column footer (Shop / Help / About) + newsletter signup, styled with `scheme-2`.

- [ ] **Step 1: Configure footer in the editor**

Theme editor → Footer: color scheme `scheme-2`; add menu blocks "Shop", "Help", "About" (create the menus under Navigation); enable the email signup block ("Join Cute Club"); enable payment icons.
Expected: footer shows 3 link columns + newsletter form.

- [ ] **Step 2: Lint + preview**

Run: `shopify theme check` → no new offenses. Refresh → blush footer with columns + signup.

- [ ] **Step 3: Checkpoint**

```bash
shopify theme push --store dev-store-3kajxn4y.myshopify.com --theme "Only Cute Stuff (dev)"
```

---

### Task 12: Core templates restyle pass (product / collection / cart / search / 404)

**Files:**
- Modify: `config/settings_data.json` (section color schemes for `main-product`, `main-collection-product-grid`, `main-cart-*`, `main-search`, `404`)

**Interfaces:**
- Consumes: schemes + CSS from Tasks 3–4. No new sections.

- [ ] **Step 1: Apply schemes**

In the editor, open Product / Collection / Cart / Search / 404 templates and set each main section's color scheme to `scheme-1` (cream) for consistency; confirm the rounded-card CSS applies to product cards and the cart drawer.
Expected: all five templates share the kawaii look.

- [ ] **Step 2: Functional click-through**

In preview: open a product → select variant → Add to cart → open cart drawer → proceed to checkout entry; run a search; visit a bad URL for 404.
Expected: every step works (Dawn behavior intact) and is styled.

- [ ] **Step 3: Lint**

Run: `shopify theme check`
Expected: no new offenses.

- [ ] **Step 4: Checkpoint**

```bash
shopify theme push --store dev-store-3kajxn4y.myshopify.com --theme "Only Cute Stuff (dev)"
```

---

### Task 13: Final verification + go-live (gated)

**Files:** none (publishing only).

- [ ] **Step 1: Full lint**

Run: `shopify theme check`
Expected: no errors (warnings acceptable; note any).

- [ ] **Step 2: Full manual QA on the dev store**

Walk home → each homepage section → collection → product → cart → checkout entry → search → 404, on **desktop and mobile widths**.
Expected: all sections render, links resolve, cart works, layout is responsive.

- [ ] **Step 3: Quick perf/a11y sanity**

Run Lighthouse (Chrome devtools) on the preview homepage.
Expected: no major regressions vs Dawn baseline; fix obvious image-size/contrast flags.

- [ ] **Step 4: GO-LIVE GATE — get explicit user approval before this step.**

After approval, push to the live store as an unpublished theme:
```bash
shopify theme push --unpublished --store only-cute-stuff-2.myshopify.com --theme "Only Cute Stuff"
```
Then publish from the live store admin → Online Store → Themes → Publish, **or** with approval:
```bash
shopify theme push --live --store only-cute-stuff-2.myshopify.com --theme "Only Cute Stuff"
```
Expected: theme live on only-cute-stuff-2. ⚠️ Live store needs real products/collections to look complete (owner's responsibility).

---

## Self-Review

**Spec coverage:** Architecture/workflow → Task 1; sample data → Task 2; visual system (palette/fonts) → Task 3; motifs/CSS → Task 4; announcement bar → Task 5; header → Task 6; hero → Task 7; collections grid → Task 8; trending → Task 9; testimonials → Task 10; footer → Task 11; core templates → Task 12; testing + go-live → Task 13. Out-of-scope items (loyalty, Instagram, brand-story/FAQ, real content) intentionally absent. All spec sections covered.

**Placeholder scan:** No TBD/TODO; all section Liquid is complete; index.json fragments are concrete. Empty `"collection": ""` in Task 9 is intentional (bound via editor in the same task).

**Type/name consistency:** Section types `cute-hero`, `cute-collection-cards`, `cute-testimonials` and block types `collection_card`, `testimonial` are used identically in their `{% schema %}`, the section Liquid, and the `templates/index.json` entries. Color scheme IDs `scheme-1/2/3` consistent across Tasks 3–12. CSS classes `.cute-card`, `.kawaii-glyph`, `.cute-section-title` defined in Task 4 and reused in Tasks 7/8/10.
