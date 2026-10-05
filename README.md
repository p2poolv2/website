# P2Poolv2 website

The landing page at the root of the P2Poolv2 project. Bootstrap 5.3 CSS
plus one small stylesheet. No build step and no npm, and nothing loaded
from a third party: the page renders with the network down. The only
JavaScript is Bootstrap's own bundle, vendored, used for the navbar
collapse.

```
index.html                 the page
styles.css                 the theme, the type scale, and the bespoke bits
assets/bootstrap.min.css   Bootstrap 5.3.3 CSS, vendored
assets/bootstrap.bundle.min.js  Bootstrap 5.3.3 JS, vendored, for the navbar
assets/fonts.css           generated @font-face rules, do not hand-edit
assets/fonts/              Inter and JetBrains Mono woff2, plus licences
assets/logo.png            the brand-pack lockup, used in the footer
assets/logo.svg            the same lockup as vector, master copy
assets/mark.svg            the brand-pack mark, used as the favicon
AGENTS.md                  conventions, and where the wiki and repo live
```

## Working on it

Open `index.html` in a browser, or serve the directory:

```
python3 -m http.server -d . 8000
```

## Deploying

GitHub Pages serves the site at `https://p2poolv2.org/`.
`.github/workflows/pages.yml` publishes it on every push to `main`, and
can be run by hand from the Actions tab. Push to deploy.

The workflow publishes `index.html`, `styles.css` and `assets/`, and
nothing else, so the Markdown files are never served. It fails the
deploy if the page gains a `<link>`, `<script>` or `@import` that loads
from another host.

The custom domain is set in the repository's Settings, Pages, with
Source set to "GitHub Actions". A workflow deploy ignores a `CNAME`
file, so that setting is the only place the domain lives. DNS points
the apex at GitHub with `A` records `185.199.108.153`,
`185.199.109.153`, `185.199.110.153` and `185.199.111.153`, and `AAAA`
records `2606:50c0:8000::153` and `2606:50c0:8003::153`.

## Conventions

- Bootstrap is themed through its own CSS variables, all set in the
  `:root` block of `styles.css`. Prefer a Bootstrap utility class over a
  new rule.
- Colours, type and logo come from `../logos/p2poolv2-brand-pack`: Bitcoin
  Orange `#F7931A` on black, with white as the only other mark. The pack's
  four colours are the only ones on the page, they all live in the `:root`
  block of `styles.css`, and any shade is one of them at reduced opacity.
  See `AGENTS.md` before adding a colour.
- Type is JetBrains Mono for headings and data, Inter for prose.
- The share chain figure in the hero is inline SVG in `index.html`, and the
  only animated thing on the page. It stays still under
  `prefers-reduced-motion`.
- Claims about the chain come from the wiki and `docs/`. Keep them in step
  when the protocol changes, in particular the version, the testnet4 status,
  and the numbers in the "If you mine" section.
