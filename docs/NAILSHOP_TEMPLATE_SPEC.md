# Nail Shop Website: Agent Template Spec

Purpose: a self-contained brief for an AI coding agent to build (or adapt) a static, multi-language nail studio website using the Beauty World design as the standard template. Shop details, content, languages and colours may change; preserve the layout, visual identity and animations described below unless the user explicitly requests a departure.

Reference implementation: this repository (`index.html`, `src/pages/`, `src/styles/main.css`, `src/scripts/common.js` and `src/scripts/home.js`). When available, use it as the visual and motion reference, including its responsive layouts and timings. Adapt the shop data and theme rather than redesigning it. Do not copy bugs, unverified business claims or accessibility failures. When the reference is unavailable, reproduce it from the detailed rules in this spec.

---

## How to use

Prompt template:

```text
Read docs/NAILSHOP_TEMPLATE_SPEC.md and build the website in an empty folder / adapt the existing one.
Overrides:
- Shop name: Studio Aurora
- Languages: de (default), en, fr   (remove vi)
- Phone: +49 30 1234567
- Email: hello@aurora.example
- Address: Hauptstraße 5, 10115 Berlin
- Opening hours: Mon-Fri 09:00-18:00, Sat 10:00-14:00, Sun closed
- Prices: Manicure 25 EUR, Gel polish 35 EUR, Refill 40 EUR ...
- Colour theme: sage green and gold instead of blush and rose, keeping the template design and animations
- Logo: images/logo.svg   (optional, keep the same header layout)
- Social: add Instagram link
- Booking: Planity link https://www.planity.com/...
- Images: folder with photos provided (cover image + gallery)
- Reviews: pasted real reviews (with translations)
- Services: besides nails, also offer eyelash extensions, waxing and massage (price categories + wording adapted)
```

Rules for the agent:

1. **Complete intake for a new project**: When asked to use this spec for a new project, collect the information in every step below unless the user has already explicitly provided it or says to use a default. Ask about optional features too, so the user can choose them or decline them; do not silently assume an optional feature is unwanted. The Overrides block above is only an example, not project data.
2. **Step-by-step interactive workflow**: Work incrementally. At each step, ask for all inputs for that step that the user has not already supplied, then wait for the user's response before implementing or moving to the next step. Do not build everything silently in one batch. Follow the development steps outlined below.
3. Apply every override. If an answer is contradictory or unclear, ask the user to resolve it rather than guessing.
4. If the user explicitly asks to skip questions or use the defaults, follow that direction and clearly identify any remaining placeholders.
5. Do not add features outside this spec, and never add the excluded features below.
6. **The Beauty World look and motion are the template**, not a menu of alternative styles. Ask the [look & feel intake](#look--feel-intake) questions about colours and branding, follow the [Design](#design) and [Motion catalogue](#motion-catalogue), and avoid [Known pitfalls](#known-pitfalls-learned-from-earlier-builds). Change the design or remove an effect only on explicit request or to respect accessibility preferences.
7. **Always look at the result.** After building, render every page in a real browser (browser tools if available, otherwise ask the user to permit a local server plus a screenshot tool), inspect screenshots, read the browser console, and fix what looks cheap, cramped, empty or broken before reporting. Never report a page as done on the basis of syntax checks alone.
8. **If the question dialog is cancelled or unavailable**, do not guess silently: re-ask the same questions as a compact numbered list in chat with the default marked, or, if the user pasted data instead, use it, list every assumption you made, and mark every remaining placeholder.
9. **Show, then refine.** After the first full render, summarise the result, show or describe the screenshots, and ask whether the colours or shades need adjustment. Do not propose a different layout or motion style as a routine choice.

---

## Step-by-step development process (agent must follow)

At each step, the agent must inspect current state, ask the user for any needed data or choices if not yet provided, apply the changes, validate them, and confirm before moving forward:

1. **Step 1 — Shop identity & contact**:
   - Ask or verify: studio name, phone number, physical address, 7-day opening hours.
   - Ask for optional contact links: email, social profiles (Instagram, Facebook, TikTok), map URL, and an external booking page (Planity, Treatwell, etc.). Ask whether there are other official links the user wants included; if so, add an appropriate data field and render it as a safe, accessible link.
   - Strip URL tracking parameters (`igsh`, `utm_*`, `fbclid`) from social URLs. For external links, allow only `https://` and open in a new tab with `rel="noopener noreferrer"`.
   - Ask which languages to offer (default: German as default language, English, Vietnamese) and which one is the default.
   - Ask for a short slogan or tagline if the owner has one (otherwise use the content default), and whether the owner wants to be named on the About text.
   - Update `src/data/shop.json`, page titles, and header brand references.

2. **Step 2 — Services & scope beyond nails**:
   - Ask whether the salon offers services beyond nail care (lashes, eyebrows, waxing, massages, facials, etc.).
   - Ask which services and prices should appear; do not invent prices. Use clearly marked placeholders only if the user agrees.
   - Adapt `hero.eyebrow`, `hero.tagline`, and `about.text` across all supported languages in `src/scripts/translations.js`.
   - Update `src/data/prices.json` with service categories and items (or collect price inputs).

3. **Step 3 — Images & gallery**:
   - Ask for or inspect supplied image files.
   - Clean filenames to URL-safe names (e.g., `nails-01.png`), pick a suitable landscape `cover.png` for the gallery banner, and populate `src/data/gallery.json`.
   - Ask whether the shop has a logo (SVG preferred) to use in the existing header. Supplied photos belong in the gallery; retain the animated nail-art hero unless the user explicitly requests otherwise.
   - If there are no photos yet, say so and keep the designed empty states (see [Design](#design)); tell the user the site looks best once real photos are added.

4. **Step 4 — Customer reviews**:
   - Ask for real customer review text, reviewer names, and a source link or permission to look them up. If the source cannot be accessed or verified, ask the user to paste the reviews; never invent or paraphrase reviews as real. Include only reviews the user selects.
   - Provide translations across all active languages in `src/data/reviews.json` (`text` as `{ "de", "en", "vi" }`).

5. **Step 5 — Look & feel**:
   - Run the [look & feel intake](#look--feel-intake) for colours, shades and branding. Keep the template typography, shapes, ornaments, header, hero and section arrangement.
   - If photos or a logo were supplied, inspect them and recommend a palette that matches; offer it as the first option.
   - Apply the answers through CSS variables on `:root` in `src/styles/main.css` first; only add component CSS where a variable cannot express the choice.
   - Render, screenshot, and review with the user (rule 9) before moving on.

6. **Step 6 — Motion & interactivity**:
   - Implement the complete [Motion catalogue](#motion-catalogue) on every site without asking users to choose a motion level. Honour explicit requests to change effects and always respect `prefers-reduced-motion`.
   - Verify that animations finish visibly, decorative effects do not obstruct content and reduced-motion mode remains usable.

7. **Step 7 — Copy, tone & optional sections**:
   - Ask for tone (warm-formal default, playful, minimal) and for the owner's story: years of experience, specialities, languages spoken, what makes the studio special. Write the About text from these facts only; do not invent credentials.
   - Ask which home sections to keep: announcement, gallery, reviews, about (each can be hidden); and whether a homepage notice (holiday closure, promotion) is needed now.
   - Ask about extra pages (FAQ, Impressum/Privacy, Services) and offer them; do not invent legal text.

8. **Step 8 — Final validation & summary**:
   - Run the validation checklist (JSON validity, script checks, translation parity, link checks, browser console, visual QA at all breakpoints).
   - Report placeholders, external links, machine translations, and anything not visually verified.

---

## Scope

### Included (public, user-facing)

- Home page, price list page, contact page.
- Language switcher (default German).
- Gallery banner with overlay, review slider, About text, contact details with opening hours, optional homepage announcement.
- Phone call links (`tel:`) as the only way to get in touch for visits.

### Excluded (do not build)

- Booking, appointment forms, calendars, schedulers, availability.
- Admin pages, login, dashboards, staff management.
- Any backend, database, API or server code. The site must be fully static.
- Contact forms that send data (a `mailto:` link is allowed if an email is provided).
- Third-party trackers, analytics, cookie banners that are not needed. Do not load external fonts or scripts (privacy, GDPR); use system/fallback fonts.

Where a booking CTA would normally be, use a "Call now" button.

Exception: if the user supplies an external booking page (e.g. Planity, Treatwell, Google Reserve), set it as `bookingUrl` in `shop.json` and render a prominent "Book online" primary button linking to it (new tab, `rel="noopener noreferrer"`, `https://` only). The site still builds no booking functionality of its own; the call button stays as the secondary (outlined) action. Mention to the user that visitors leave the site and the third party's privacy policy applies. Without `bookingUrl`, the button is hidden and the rules above apply unchanged.

---

## Tech stack and conventions

- Plain HTML, CSS and vanilla JavaScript. No framework, no build step, no dependencies.
- Must work when served by any static file server (`python3 -m http.server`). Content is loaded with `fetch` from JSON, so `file://` is not supported; say so in the README.
- Shared code in one script, page-specific code in separate scripts, one stylesheet, content in JSON files, UI strings in one translations file.
- Relative paths only, so the site works from the server root. Pages in subfolders use a `data-root` attribute on `<body>` (e.g. `../../`) to resolve data and links.
- Text from data files is always inserted with `textContent` (never `innerHTML`) to avoid injection.
- Semantic HTML, `lang` attribute updated on language change, `aria-label`s on icon-only buttons, visible focus styles, `prefers-reduced-motion` respected, entrance animations must always end visible.
- Responsive: desktop, tablet, phone. Root font size 100%, 112.5% from 1280px, 125% from 1680px; size everything in `rem`.
- 2-space indent, UTF-8, LF line endings.

---

## File structure

```text
index.html                    Home
src/pages/prices.html         Price list
src/pages/contact.html        Contact and opening hours
src/scripts/translations.js   window.TRANSLATIONS = { de:{}, en:{}, ... }
src/scripts/common.js         Header, footer, language switch, shop data, contact block
src/scripts/home.js           Announcement, gallery overlay, review slider
src/scripts/prices.js         Price list rendering
src/styles/main.css           All styles + CSS variables
src/data/shop.json            Name, phone, email, address, hours, social
src/data/reviews.json         Reviews
src/data/gallery.json         Cover + image list
src/data/announcement.json    Optional homepage notice
src/data/prices.json          Price list
images/                       Photos (gallery cover and gallery images)
README.md  .gitignore  .gitattributes  .editorconfig
```

---

## Navigation

Sticky header on every page. Tabs, in order:

| Tab (DE / EN / VI) | Target |
| --- | --- |
| Startseite / Home / Trang chủ | `index.html` |
| Preisliste / Price list / Bảng giá | `src/pages/prices.html` |
| Kontakt / Contact / Liên hệ | `src/pages/contact.html` |

- Brand name (from `shop.json`) links to home. Current tab has `aria-current="page"`.
- Language buttons are flags (inline SVG data URIs, no image files) with the active one highlighted.
- Footer: `© <year> <shop name>. <All rights reserved.>` (translated).
- Header and footer are rendered by `common.js` into `#site-header` and `#site-footer` so they are not duplicated per page.
- Mobile (<= 900px): brand and flags on one row, nav tabs on a second row; the brand must never wrap.

---

## Languages (i18n)

- Default: German (`de`). Others by default: English (`en`), Vietnamese (`vi`). The user may add or remove languages in the overrides.
- Selected language is saved in `localStorage` under `lang`; fall back to `de` if the value is unknown.
- All UI strings live in `translations.js` under flat keys (`nav.home`, `hero.cta`, ...). Elements use `data-i18n="key"` (text) and `data-i18n-label="key"` (aria-label).
- Every language must have exactly the same keys. A missing key falls back to German, then to the key itself.
- Data files with translatable text use an object per language: `{ "de": "...", "en": "..." }`. Reading code uses the current language, falling back to German.
- Weekday names are generated with `toLocaleDateString(locale, { weekday: "long" })` using locales `de-DE`, `en-GB`, `vi-VN` (add one per new language).
- The page `<title>` is `<Page title> – <Shop name>` (home: shop name only) and updates on language change.
- To add a language: add its translations block, a flag button + CSS flag, a locale mapping, and translate every data file field. Machine translations are acceptable but must be flagged to the user for review.

### UI string keys (all languages must define these)

```text
nav.home nav.prices nav.contact lang.label
hero.eyebrow hero.tagline hero.cta book.cta
notice.title
gallery.title gallery.empty gallery.close
reviews.title reviews.stars reviews.prev reviews.next reviews.more reviews.less
about.title about.text
contact.title contact.intro contact.phone contact.address contact.social contact.call
hours.title hours.closed
prices.title prices.intro prices.empty
footer.rights
```

Add `contact.email` if an email is configured, and `contact.map` ("Open in maps") if `mapUrl` is configured.

---

## Pages and behaviour

### Home (`index.html`, `<body class="home">`)

Sections in this order:

1. **Hero**: small uppercase eyebrow ("Nagelstudio"), large shop name (`h1`), tagline, "Call now" pill button (`tel:` link built from `shop.phone`). If `bookingUrl` is set, a "Book online" primary pill button sits before it and the call button turns outlined. Right side: decorative artwork of five polished nails drawn with CSS inside a double gold-line arch, with a soft glow and twinkling sparkles (see [Hero artwork](#hero-artwork-default-style)). On mobile the art moves above the text and shrinks.
2. **Announcement** (optional): hidden unless the current language's text in `announcement.json` is non-empty. Shows small label ("Hinweis") and the message in a large display serif; preserves line breaks.
3. **Gallery banner**: full-width clickable button, `min-height ~18rem`, with `cover` image under a dark tint and a centred pill-outlined "Galerie" title. Hover: gentle zoom and lighten. Click opens an overlay (modal) with a grid of all images; closes via the close button, backdrop click or Esc; page scroll is locked while open. If `images` is empty the overlay shows "Pictures coming soon". If `cover` is empty the banner shows a solid dark colour.
4. **Reviews**: section hidden when `reviews.json` is empty. A slider (one review per slide) with prev/next buttons, wrap-around, and touch swipe (threshold 40px). Each review: five gold stars (accessible label "5 out of 5 stars"), quote, name. Review text is clamped to 5 lines; a "Read more" / "Show less" toggle appears only if the text overflows, re-checked on resize and language change.
5. **About**: heading on the left, text on the right (stacked and centred on mobile). Text is a short, personal story of the owner (see Content).
6. **Contact**: heading plus the shared contact block (details left, thin vertical divider, opening hours right; stacked with a horizontal divider on mobile).

### Price list (`src/pages/prices.html`)

- Title, intro line, then one card per category with a heading and rows of `name ... price`.
- Category and item names are per-language objects; `price` is a plain string (so currency formats like `25 €`, `from 30 €`, `€25` all work).
- Empty list shows "price list coming soon".
- Re-renders on language change.

### Contact (`src/pages/contact.html`)

- Title, intro ("We look forward to your call..."), the shared contact block (phone as `tel:` link, address, opening hours, plus email / social links when configured), and a "Call" button.
- No form.

### Shared contact block

- Phone: link `tel:` with non-digit characters (except `+`) stripped.
- Address: one line per entry in the `address` array.
- Opening hours: 7 rows Monday to Sunday, localised weekday names, `null` renders the translated "Closed". Small tabular-number text.
- All values inserted as text.

---

## Data schemas

### `shop.json`

```json
{
  "name": "Nagelstudio 27",
  "phone": "+49 000 0000000",
  "email": "",
  "address": ["Musterstraße 27", "00000 Musterstadt"],
  "openingHours": ["10:00 – 18:00", "10:00 – 18:00", "10:00 – 18:00", "10:00 – 18:00", "10:00 – 18:00", "10:00 – 14:00", null],
  "social": { "instagram": "", "facebook": "", "tiktok": "" },
  "mapUrl": "",
  "bookingUrl": ""
}
```

- `openingHours`: exactly 7 entries, Monday first. Split shifts as a single string, e.g. `"09:00 – 12:00, 14:00 – 18:00"`.
- `email`, `social`, `mapUrl`, `bookingUrl` are optional. The reference implementation renders `name`, `phone`, `address`, `openingHours`, `social` (Instagram, Facebook, TikTok links in the contact block under a translated "Social media" heading; render each link with its inline SVG icon followed by the page handle/name, e.g. `<svg> @lt.nails.nurnberg`, clickable as a single link opening in a new tab with `rel="noopener noreferrer"`; only `https://` URLs are rendered) and `bookingUrl` (see Scope). Strip tracking parameters (`igsh`, `utm_*`, `fbclid`) from profile URLs the user supplies, and mention that you did. If the user supplies the other optional fields, the agent must render them: email as a `mailto:` link in the contact block; `mapUrl` as an "Open in maps" link (do not embed third-party iframes unless the user asks, and then mention the privacy implication).

### `reviews.json`

```json
[{ "name": "Anna M.", "text": { "de": "Bewertungstext.", "en": "Review text.", "vi": "Nội dung đánh giá." } }]
```

Reviews are translated like other data files: `text` is a per-language object, `name` is a plain string. A plain string `text` is also accepted and shown in every language. Keep the original wording and language of each real review in its own language key, and flag any machine translations to the user for review. Use realistic placeholders, clearly flagged as placeholders, and never present invented reviews as real.

### `gallery.json`

```json
{ "cover": "images/nails-01.png", "images": ["images/nails-01.png", "images/nails-02.png"] }
```

Paths are relative to the site root. Add `alt` support only if requested.

If the user provides pictures (an `images/` folder or uploaded files), the agent must integrate them:

1. Rename files with spaces, parentheses or other special characters to clean, lowercase, URL-safe names (e.g. `Pasted image (3).png` becomes `nails-03.png`). Keep the original extension and tell the user about the renames.
2. List every picture in `images`, in numeric/alphabetical order; pick one landscape picture as `cover` (it may also appear in `images`).
3. Do not re-encode, crop or resize the files; the overlay grid crops to squares with CSS (`object-fit: cover`), and images use `loading="lazy"`.
4. If a picture is large (over about 1 MB), mention it to the user as a performance note.

### `announcement.json`

```json
{ "text": { "de": "", "en": "", "vi": "" } }
```

### `prices.json`

```json
[
  {
    "category": { "de": "Maniküre", "en": "Manicure", "vi": "Làm móng tay" },
    "items": [
      { "name": { "de": "Gel-Lack", "en": "Gel polish", "vi": "Sơn gel" }, "price": "35 €" }
    ]
  }
]
```

---

## Content defaults (German first; translate to every language)

Tone: warm, personal, professional, no hype. Address visitors formally in German ("Sie").

- **Tagline:** Pflege, Schönheit und individuelles Nageldesign
- **Hero CTA:** Jetzt anrufen; booking CTA (only with `bookingUrl`): Online Termin buchen
- **About:** Write a short, warm introduction using only facts confirmed by the owner. Invite visitors to call for personal service. Do not invent experience, ownership details, treatments or other services.
- **Contact intro:** We look forward to your call. For questions, requests and arranging a visit, reach us by phone.
- **Services beyond nails:** The studio is nail-focused by default, but the user may state in the overrides that it offers other services too (e.g. eyelashes, brows, waxing, massage, facials). If so, add them as extra categories in `prices.json` and adapt `hero.eyebrow`, `hero.tagline` and `about.text` in every language so they no longer describe nails only, keeping the "call us" wording. Mention only services the user confirms.
- **Price intro:** Prices at a glance; for individual requests, call us.
- **Typical price categories (suggestions):** Manicure, Pedicure, Gel polish, Nail extensions (new set, refill), Nail art, Removal. Only include what the user supplies; use obvious placeholder prices (`00 €`) only with the user's agreement. Otherwise leave the price list empty.

Do not mention online booking, appointments via a form, or an admin anywhere in the copy (the optional external "Book online" button from `bookingUrl` is the only exception). Where visits are discussed, direct people to call.

---

## Look & feel intake

Ask these together in one batch (choice-style questions if the tool exists, otherwise a numbered chat list). Offer "use the default" and "recommend one for me". The answers customize the existing design; do not offer alternative typography, layouts, hero designs, ornaments or motion levels as standard choices.

| # | Question | Options (default first) | Lands in |
| --- | --- | --- | --- |
| 1 | Palette | Blush & Rose Gold, Sage & Gold, Nude & Champagne, Lavender & Silver, Custom (hex values or logo colours) | colour variables |
| 2 | Colour direction | Lighter or deeper shades, preferred accent and metal tone, or colours from a supplied logo | colour variables and nail gradients |
| 3 | Logo | Text brand or logo image from `images/` (alt text = shop name), retaining the header layout | header |
| 4 | Favicon and browser theme colour | Generated initial in the accent colour, supplied favicon, or none | `<head>` of every page |

Palette starting points (`rose` = primary accent, `rose-dark` = hover/links/prices). Check and adjust each actual foreground/background combination, including buttons and small gold labels, to meet WCAG AA; the values below are not a guarantee of contrast:

| Preset | blush | cream | rose | rose-dark | gold | gold-light | plum | muted |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Blush & Rose Gold (default) | #f4e8e3 | #fcf8f4 | #a35a66 | #8a4655 | #b8956a | #e6d2b5 | #2e2227 | #6f6064 |
| Sage & Gold | #e9efe6 | #f8faf5 | #6f8a6b | #566f53 | #b8956a | #e6d2b5 | #25302a | #637067 |
| Nude & Champagne | #efe6dc | #faf6f0 | #9a7b5f | #7d6248 | #c5a572 | #ead9b9 | #2f2620 | #6e6258 |
| Lavender & Silver | #ece8f2 | #f8f6fb | #7c6a9c | #66557f | #a8a2b5 | #d9d5e3 | #2a2533 | #6a6574 |

Dark themes (e.g. Noir & Gold) are allowed only if the user asks: first introduce role tokens (`--surface`, `--text`, `--footer-bg`), stop hard-coding `#fff` or `--plum` for surfaces and text, then verify contrast on every component.

Design review loop: after applying the answers, screenshot Home, Prices and Contact at desktop and phone width, compare them with the reference, show or describe them, and ask whether the palette or shades need adjustment.

---

## Design

Feel: a calm, feminine, luxurious nail studio. Airy, generous whitespace, soft colours, thin lines, rounded cards, elegant serif headings with spaced uppercase small labels. Inspiration: high-end salon sites (blush/nude palettes, rose-gold accents, minimal ornament, big imagery).

### Design tokens (CSS variables on `:root`)

```css
--blush: #f4e8e3;       /* tinted section background */
--cream: #fcf8f4;       /* page background */
--rose: #a35a66;        /* primary accent, buttons */
--rose-dark: #8a4655;   /* hover, links, prices */
--gold: #b8956a;        /* hairlines, ornaments, stars, labels */
--gold-light: #e6d2b5;  /* glows, sparkles, banner frame */
--plum: #2e2227;        /* text, footer */
--muted: #6f6064;       /* secondary text */
--line: rgba(46, 34, 39, 0.12);
--surface: #fff;        /* card background (never hard-code #fff in components) */
--radius-card: 20px;
--radius-btn: 999px;
--serif: "Cormorant Garamond", "Playfair Display", "Iowan Old Style", "Palatino Linotype", Palatino, "Book Antiqua", Georgia, serif;
--sans: "Helvetica Neue", "Segoe UI", Roboto, "Noto Sans", "Liberation Sans", Arial, sans-serif;
--shadow: 0 18px 50px rgba(90, 50, 62, 0.1);
```

To re-theme, change only these variables (and the hero nail gradients); do not scatter literal colours or radii in components. Keep text/background contrast at WCAG AA or better.

### Typography

- No external fonts. Every font stack must end with a generic family (`serif` / `sans-serif`) and list real, widely installed fonts before it. Do not rely on `system-ui` (it resolved to a monospace font on a Linux machine); verify in the browser that body text is not monospace.
- Headings, tagline, About text, contact values and review text: serif. Labels, nav, buttons, hours, small print: sans, uppercase, `0.18em`-`0.4em` letter-spacing, `0.68rem`-`0.8rem`.
- Hero name `clamp(3.2rem, 7vw, 5.2rem)`, tagline italic serif about `1.5rem`, section titles `2.6rem` (home `3rem`), body line-height about `1.7`.

### Component rules

- **Header:** sticky, translucent cream with blur, 1px bottom line. Desktop: CSS grid `1fr auto 1fr` so the nav is truly centred (brand left, flags right). Brand serif, uppercase, `0.22em` spacing, never wraps. Nav links small uppercase `0.22em`; a gold underline grows from the centre on hover and for `aria-current`. Mobile (<= 900px): flex-wrap, brand and flags on row one, nav on row two.
- **Language flags:** small circles (about `1.5rem`) from inline SVG data URIs with `preserveAspectRatio` set so they fill the circle; inactive at 50% opacity, active with a cream-then-gold double ring.
- **Buttons:** `--radius-btn`, accent background, white uppercase text with `0.2em` spacing, soft coloured shadow, lift on hover. Outline variant for the secondary action. `overflow: hidden` with a light-sweep pseudo-element (see Motion).
- **Section titles:** serif, centred, with an ornament beneath: two gold hairlines fading outwards with a small diamond between them. The diamond is a pseudo-element and must be hidden from assistive tech (`content: "\25C6" / ""`, with a plain `content` fallback line before it) so it never appears in the heading's accessible name. About title is left-aligned on desktop.
- **Cards** (reviews, contact, price categories, overlay): `--surface`, 1px gold hairline at about 25% opacity, `--radius-card`, `--shadow`.
- **Hero:** see [Hero artwork](#hero-artwork-default-style). Blush background with two soft radial gradients (champagne top-left, rose bottom-right) and a thin gold bottom border. Generous padding (about `7rem` vertical on desktop).
- **Gallery banner:** about `16rem` tall. With a cover image: image under a dark tint. Without one: a designed fallback (dark aubergine gradient with a soft gold glow), a thin inset gold frame, and the pill-outlined title. Never a flat, empty dark block.
- **Reviews:** cards with five gold stars, large serif quote, small uppercase name. Section hidden when empty.
- **About:** large serif paragraph (about `1.55rem`) with a thin gold vertical divider on its left (removed and centred on mobile).
- **Contact card:** labels small uppercase gold `0.3em`; values serif about `1.4rem`; phone number larger (`1.8rem`); a vertical gradient hairline divider between details and hours (horizontal on mobile); hours rows separated by `--line`, closed days muted italic. "Open in maps" is a small uppercase link with an arrow, spaced close to the address it belongs to.
- **Sections:** alternate cream and blush (hero blush, reviews blush, About cream, Contact blush). Section padding `6.5rem` desktop, `4rem` mobile.
- **Prices / empty states:** price rows use dotted leaders to the price. The empty-prices message sits in a designed card in italic serif, not bare text.
- **Footer:** plum background, small uppercase light text, 1px gold top border.
- Breakpoints: 900px (header compact), 800px (hero stacks), 700px (sections and contact stack).

### Hero artwork (default style)

- A `24rem` square container with a gold-line arch top (`border-radius: 12rem 12rem 0 0`) and a second, fainter inner arch; bottom edge closed.
- Five nails fanned from a distant common pivot (`transform-origin` about `32rem` below each nail, rotations about -17deg, -8.5deg, 0, 8.5deg, 17deg, set through a `--r` variable). Each nail is a real nail shape, not a capsule: rounded top, squarer base (`border-radius: 50% 50% 18% 18% / 34% 34% 8% 8%`), gradient colour, inset shadow, a gloss streak and a pale cuticle half-moon. Nails must be spaced so they read as five separate nails and fit inside the arch with margin.
- A blurred champagne glow behind the nails and six small four-point sparkles (CSS `clip-path` stars) around and on the arch edge.
- Decorative only: `aria-hidden="true"` on the container. No image files.
- Keep this artwork as the hero on every site; gallery photos do not replace it unless explicitly requested.

### Motion catalogue

The following complete motion treatment is part of the standard template for every shop. Preserve the reference's pacing and placement; adjust colours to the chosen palette, not the character of the effects. Only deviate for an explicit request or reduced-motion preferences.

- Hero intro: eyebrow, name, tagline and buttons fade up in sequence (about 0.12s apart); the eyebrow hairline draws in.
- Hero art: nails rise and fade in one by one, then float gently (6-7s loop, offset per nail); each gloss streak pulses; the glow breathes; sparkles twinkle (scale, rotate, fade) on staggered delays.
- Buttons: a diagonal light sweep on hover; the primary hero button also sweeps by itself every few seconds.
- Gallery banner: a slow diagonal sheen crosses it; a cover image drifts with a very slow zoom; hover tightens the frame and zooms slightly.
- Scroll reveal: sections fade and slide up (about 1.6rem) when they enter the viewport; title hairlines grow outwards; contact rows and opening-hours rows stagger in.
- Language flags and slider buttons lift slightly on hover.
- **Ambient background (every page):** a fixed, `aria-hidden`, `pointer-events: none` layer injected by `common.js` at `z-index: -1`, containing four large soft colour orbs (gold-light, rose, gold, rose; built with `color-mix` from the palette variables) that drift slowly (26-36s, alternate) and shift with scroll at different speeds (parallax via a `--sy` custom property and the `translate` property), plus about 16 gold glitter dots and four-point stars that rise slowly with a gentle sway (16-26s loops, negative delays so the screen is already populated on load, half hidden on phones). Sections with a tinted background use `--blush-soft` (blush at about 78% opacity, with a solid fallback inside an `@supports` check) so the layer shows through. About, Contact (tinted section) and every sub-page title area also get a slow rotating comet ring (conic-gradient arc masked to a 1px ring, `z-index: -1` inside an `isolation: isolate` section, clipped so it never causes horizontal scroll). With reduced motion the glitter is not created and the orbs stay still.

Rules:

- Everything stays visible without JavaScript: the scroll-reveal hidden state is applied by a class added from JS (`.reveal`, then `.in` through `IntersectionObserver`; skip entirely if the API is missing). Entrance animations use `animation-fill-mode: both` and always end visible.
- Stack independent effects on independent properties (`translate`, `scale`, `rotate`, `opacity`, `transform`) so they do not override each other (e.g. float uses `translate`, rotation uses `transform`).
- Do not animate layout properties except in one-time entrances. Infinite loops are for decorative elements only and must be slow and low-contrast.
- With `prefers-reduced-motion: reduce`, explicitly stop decorative loops, parallax and scroll-reveal movement; show content and artwork immediately in their final visible states. Respect changes to the preference while the page is open.
- Total intro choreography under about 1.5s; nothing may delay access to the call button.

---

## Customisation cheatsheet (where each override goes)

| Override | Edit |
| --- | --- |
| Name, phone, email, address, hours, social, booking link | `src/data/shop.json` |
| Add / remove a language | `translations.js`, flag buttons and CSS flags, locale map in `common.js`, every per-language field in `prices.json` and `announcement.json` |
| Prices | `src/data/prices.json` |
| Services beyond nails | categories in `src/data/prices.json`; `hero.eyebrow`, `hero.tagline`, `about.text` in `src/scripts/translations.js` |
| Reviews | `src/data/reviews.json` |
| Photos | put files in `images/` (clean file names), list them in `src/data/gallery.json` |
| Homepage notice (holiday closure etc.) | `src/data/announcement.json` |
| Colours and shades | CSS variables and nail gradients in `src/styles/main.css`; keep the template design and motion |
| Explicitly requested design or motion changes | component rules and the Motion block in `src/styles/main.css`; hero markup in `index.html` |
| Logo, favicon, theme colour | `images/`, `<head>` of each page, brand rendering in `common.js` |
| Wording | `src/scripts/translations.js` |
| Default language | `getLang()` fallback in `common.js` |

Requests to add pages (e.g. "Services", "FAQ", "Impressum/Privacy"): add a new HTML page under `src/pages/`, a nav entry in `common.js`, and translation keys for every language. For sites in Germany, mention to the user that a legal notice (Impressum) and privacy policy are normally required; offer to add pages but do not invent legal text.

---

## Known pitfalls (learned from earlier builds)

Check each of these before reporting; every one occurred in a real build.

1. **Shadowed helper names.** A local variable named like a helper function (e.g. `var socialLabel = ...` inside a function that also calls `socialLabel(...)`) hoists and breaks the call, and the whole contact block silently failed to render. Give variables and helpers distinct names, avoid `var` re-use of function names, and read the browser console on every page.
2. **Monospace fallback fonts.** Stacks relying on `system-ui` or missing fonts rendered body text in monospace on Linux, which made the site look cheap. Use the stacks in the design tokens, end with a generic family, and confirm computed fonts in the browser.
3. **Capsule-shaped "nails".** Plain pills look like pills. Use the nail shape, gloss and cuticle details described in [Hero artwork](#hero-artwork-default-style) and keep generous spacing.
4. **Empty gallery as a flat black slab.** The no-cover state needs the designed fallback (gradient, glow, inset frame) and a modest height.
5. **Decorative text read aloud.** Ornament glyphs in pseudo-elements leaked into heading names ("Über uns ◆"). Use the alt-text form of `content` or draw ornaments with gradients.
6. **Nav not centred.** `justify-content: space-between` with unequal side items shifts the nav. Use the `1fr auto 1fr` grid on desktop.
7. **Cramped or overlapping hero art and crowded sections.** Verify spacing visually at desktop and phone width; give sections real whitespace.
8. **Generic, flat styling.** Avoid default-looking pill-capsule art, uniform grey shadows and unstyled empty states. Use hairlines, serif display type, spaced uppercase labels and consistent gold accents.
9. **Dynamic content without i18n.** After rendering into the DOM with `innerHTML`/`createElement`, call the translation apply step again, and re-render on `langchange` (contact block, price list, reviews, announcement).
10. **Animation property clashes.** Two animations or a rotation plus a float on the same `transform` overrode each other. Use `translate`/`scale`/`rotate` for independent effects.
11. **Screenshots taken mid-animation.** Wait about 1.5s or capture twice before judging a section; scroll-reveal sections look blank until they enter the viewport.
12. **Reporting unverified work.** Do not say "done" for visuals you did not look at; list exactly what you did and did not view.
13. **Server port already in use.** Check whether the existing server on the port serves this folder (`curl` a data file) and reuse it instead of fighting for the port.
14. **Placeholder polish.** Empty prices, no reviews and no photos must still look intentional (designed empty states), and each must be listed to the user as a placeholder.

---

## Validation checklist (agent must run and report)

1. `node --check` every file in `src/scripts/`.
2. Every JSON file in `src/data/` parses (`python3 -m json.tool`).
3. Translation key parity across all languages (script comparing sorted keys).
4. Serve with a static server (reuse a running one if it serves this folder) and confirm HTTP 200 for all pages, scripts, styles and data files (only if the user permits starting a server).
5. Grep to confirm none of these exist: `booking`, `appointment`, `admin`, `termin`, `server.py`, `<form` (matches for `bookingUrl` / `book.cta` / "Termin buchen" are expected when an external booking link is configured).
6. Confirm no external requests: no `http(s)://` in scripts, styles or HTML except user-supplied links and the SVG `xmlns` namespace identifier in inline SVGs.
7. Confirm all placeholders (phone, address, prices, reviews, photos, machine translations) are listed to the user for replacement.
8. **Browser console is clean** on Home, Prices and Contact, in every language (no errors, no failed requests).
9. **Visual QA with screenshots** of Home, Prices and Contact at about 1440px, 1024px, 820px and 390px width: no overflow or horizontal scroll, header brand never wraps, nav centred on desktop, hero art fully inside its arch and not overlapping text, contact card and opening hours readable, empty states look designed, text contrast acceptable. Also check: German long words and Vietnamese diacritics do not clip; open the gallery overlay and close it with button, backdrop and Esc; step through the review slider if reviews exist; hover and keyboard-focus states are visible.
10. **Motion QA:** compare all reference effects on Home, Prices and Contact, including lower-page backgrounds; check desktop and phone widths. Verify intro and scroll reveals end visible, the call button is immediately usable, and decorative layers do not intercept interaction or create overflow. Emulate reduced motion and verify content remains visible and loops and parallax stop; report any checks not performed.
11. **Accessibility basics:** one `h1` per page, heading order sensible, icon-only buttons have `aria-label`, decorative art is `aria-hidden`, ornament glyphs do not appear in accessible names, `lang` updates on language change.
12. State clearly what was not checked visually or in which browsers.

## README requirements

The generated `README.md` must include: what the site is, page list, folder structure, requirements, run locally (`python3 -m http.server 8000 --bind 127.0.0.1`), open on another device on the same network (`--bind 0.0.0.0`, find IP with `ip -4 addr`, open `http://<host-ip>:8000/`, firewall note), "no build step", deployment to any static host, a table of where to edit content, and the validation commands. Also create `.gitignore`, `.gitattributes` (`* text=auto eol=lf`, images binary) and `.editorconfig`.
