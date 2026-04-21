# Offer Ladders

Per-client interactive offer-ladder visualizations, deployed via GitHub Pages.

Each client lives in `clients/<slug>/` where `<slug>` is `client-full-name-XXXXXX`
(kebab-cased name + random 6-digit identifier). The identifier makes URLs
effectively private by obscurity — a `noindex` meta tag and `robots.txt` further
discourage search indexing.

## Adding a new client

```bash
python offer-ladder.py --new "Client Full Name"
```

This creates `clients/client-full-name-XXXXXX/config.json` from the template.
Edit that file (tier prices, take rates, client name), then regenerate pages:

```bash
python offer-ladder.py
```

Commit + push. Share the direct URL with the client:
`https://<user>.github.io/<repo>/clients/client-full-name-XXXXXX/`

## Rebuilding all pages

```bash
python offer-ladder.py
```

Walks every `clients/<slug>/config.json`, writes a sibling `index.html` in each
folder, and regenerates the root `index.html` (a generic landing page — no
client list is exposed).

## Config schema

```json
{
  "client_name": "Display name (appears in page header)",
  "title": "Offer Ladder",
  "traffic": 1000,
  "traffic_label": "clicks",
  "checkout_label": "Entry Checkout",
  "tiers": [
    { "name": "...", "price": 17,  "take_rate": 0.10, "kind": "core" },
    { "name": "...", "price": 37,  "take_rate": 0.40, "kind": "bump" },
    { "name": "...", "price": 997, "take_rate": 0.20, "kind": "ascension" }
  ],
  "disclaimer": "..."
}
```

**Tier kinds:**
- `core` — entry product. `take_rate` = fraction of traffic that buys.
- `bump` — checkout order bump. `take_rate` = fraction of core buyers who
  also take this bump. Bumps render inside an "Entry Checkout" container
  with the core.
- `ascension` — parallel path from core (e.g., mid-ticket, high-ticket).
  `take_rate` = fraction of core buyers who choose this path. Ascension
  tiers render side-by-side.

Exactly one `core` tier is required, before any bumps or ascensions.

## Interactivity

Each client page has a right-side sidebar with editable take-rate inputs
and a Recalculate button. Inputs also recalculate on Enter. The rendered
math updates in-place — no server required, it all runs client-side.

## Privacy

- Folder names include a 6-digit random suffix so URLs aren't guessable
  from a client's name alone.
- Each client page includes `<meta name="robots" content="noindex,nofollow">`.
- `robots.txt` disallows `/clients/`.
- The root `index.html` is a generic landing page with no client list.

This is obscurity-based, not true access control. For real privacy, use a
private repo with GitHub Pro (enables private Pages) or deploy behind a
password-protected host (Netlify/Vercel).

## Layout

```
offer-ladders/
├── offer-ladder.py                 # generator
├── index.html                      # generic landing (regenerated)
├── robots.txt
├── README.md
├── .gitignore
└── clients/
    ├── _template/
    │   └── config.json             # template for new clients
    └── <client-slug>/
        ├── config.json             # hand-edited
        └── index.html              # generated
```
