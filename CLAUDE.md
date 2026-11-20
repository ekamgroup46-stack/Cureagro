# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Single-page marketing site for Cure Agro Chemicals Pvt. Ltd (Hyderabad agrochemical company). It is plain static HTML: **everything (CSS, data, JS) lives in `index.html`**, and images are in `img/`. There is no build step, package manager, linter or test suite, and no git repo.

To preview, open `index.html` directly in a browser or serve the folder, e.g. `python -m http.server 8000`. The only external dependency is Google Fonts (Sora for headings, Manrope for body text).

## Architecture

- **Product data drives most of the page.** The `P` array in the `<script>` block is the single source of truth: `[id, name, composition, categoryKey, bio?]`. These sections are rendered from it at load time: the category flip cards (`#cgrid`), the product grid with tabs and search (`#pgrid`), the product modal, and the enquiry form's product `<select>`.
  - `id` must match an image file `img/<id>.webp`, which is resolved by the `img()` helper.
  - `categoryKey` is one of `ins | fun | her | pgr`, defined in `CAT` along with the display name, chip class, card gradient and description. `bio` (5th element = 1) changes the chip to "Bio-Insecticide" (`c-bio`).
  - `feat` picks the two images shown on each category card, and `ids` in the 3D hero IIFE picks the products on the rotating ring. Both reference product ids.
- **Hardcoded counts**: the stats bar (`data-n="62"`, `40`, `9`, `13`) is static HTML, and the counter animation adds a `+` only when `n==62`. Update these by hand when you add or remove products. Tab and category counts are computed automatically.
- **Images**: product packshots are `img/<id>.webp`. Decorative and photo assets have a `_` prefix (`_drop.png` logo/favicon, `_*.jpg` used by the about stack and the gallery list `G`).
- **Contact / WhatsApp**: there is no backend. The form and the modal's "Enquire" button build `https://wa.me/<WA>?text=...` links. The number appears in the `WA` constant and is also hardcoded in the HTML (`tel:` link, floating `.wa` button, success message). Change all of these together.
- **Hero**: a CSS-3D product ring (`#ring`, positioned each frame via rAF, draggable) over a 2D canvas particle field (`#gl`). The loop pauses when the canvas is offscreen.
- **Styling**: design tokens live in `:root` CSS variables. Responsive breakpoints are at 980px and 560/600px. `.rv` elements fade in through an IntersectionObserver. `prefers-reduced-motion` turns off all animations.
- Product compositions and safety copy are regulatory/label-type content. Don't invent doses, crops or claims; the site intentionally defers to "contact us" and the product label.
