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

The site is served from `p2poolv2.org` by nginx, out of
`/var/www/p2poolv2.org`. The host keeps a checkout of this repository in
`~/website`. An Ansible playbook in `ansible/` pulls `main` there and
copies the published files into the webroot:

```
cd ansible
ansible-playbook deploy.yml --check   # dry run: what would change
ansible-playbook deploy.yml           # deploy
```

You need SSH access as `ubuntu@p2poolv2.org`. The host pulls from GitHub
with its own key, so push to `main` first; the playbook deploys what is
on GitHub, not your working tree.

Only `index.html`, `styles.css` and `assets/` are published. Anything
else in the webroot is deleted, so `.git` and the Markdown files are
never served.

The last tasks check the deploy. They fetch `https://p2poolv2.org/` and
`styles.css` over the public URL and compare each with its file on the
server, then confirm that `/.git/HEAD` returns 404. A failed check fails
the run.

The playbook does not reload nginx. nginx reads static files from disk
on each request, so new content is live once it is copied. The
`p2poolv2.org` server block belongs to the node's nginx setup, not to
this repository.

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
