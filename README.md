# Beauty World – Nagelstudio Nürnberg

Static, multi-language website for the nail studio **Beauty World** in Nuremberg.
Plain HTML, CSS and vanilla JavaScript – **no build step, no dependencies, no backend**.

## Pages

- `index.html` – home (hero, optional announcement, gallery, reviews, about, contact block)
- `src/pages/prices.html` – price list
- `src/pages/contact.html` – contact and opening hours

Languages: German (default), English, Vietnamese. The selected language is stored in
`localStorage`.

## Folder structure

```text
index.html                    Home
src/pages/prices.html         Price list
src/pages/contact.html        Contact and opening hours
src/scripts/translations.js   window.TRANSLATIONS = { de:{}, en:{}, vi:{} }
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
```

## Requirements

Any static file server and a modern browser. Content is loaded with `fetch` from JSON
files, so opening the pages via `file://` does **not** work – a server is required.

## Run locally

```bash
python3 -m http.server 8000 --bind 127.0.0.1
```

Then open <http://127.0.0.1:8000/>.

## Open on another device (same network)

```bash
python3 -m http.server 8000 --bind 0.0.0.0
ip -4 addr   # find your host IP
```

Open `http://<host-ip>:8000/` on the other device. If it does not load, check that your
firewall allows incoming connections on port 8000.

## Deployment

Upload the whole folder to any static host (Netlify, GitHub Pages, classic web space, …).
No build step is needed.

## Where to edit content

| What | File |
| --- | --- |
| Name, phone, email, address, hours, social, booking link | `src/data/shop.json` |
| Prices | `src/data/prices.json` |
| Reviews | `src/data/reviews.json` |
| Photos | files in `images/`, listed in `src/data/gallery.json` |
| Homepage notice (holiday closure etc.) | `src/data/announcement.json` |
| Wording / UI strings | `src/scripts/translations.js` |
| Colours, fonts | CSS variables on `:root` in `src/styles/main.css` |

## Validation

```bash
node --check src/scripts/*.js
for f in src/data/*.json; do python3 -m json.tool "$f" > /dev/null; done
```
# BeautyWorld
