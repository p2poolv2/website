# AGENTS.md

Guidance for AI coding agents working on the P2Poolv2 website.

This directory is the public landing page for P2Poolv2. It is plain HTML
and CSS with no build step. Everything it loads is vendored into
`assets/`, so the page makes no third-party requests and renders with the
network down. It is a separate project from the node source, and is not
part of the `p2poolv2` git repository.

## Where everything is

Local checkouts, all siblings of this directory under
`/home/kulpreet/projects/p2poolv2/`:

| What | Path | Online |
|---|---|---|
| Brand pack | `../logos/p2poolv2-brand-pack` | -- |
| Other logos | `../logos` | -- |
| Wiki | `../p2pool-v2.wiki` | https://github.com/p2poolv2/p2poolv2/wiki |
| This site, once deployed | -- | https://p2poolv2.org |
| Live testnet4 pool and chain explorer | -- | https://testnet4.p2poolv2.org |
| Public pool metrics, Grafana | -- | https://grafana.p2poolv2.org |
| Node source | `../p2pool-v2` | https://github.com/p2poolv2/p2poolv2 |

Note the local directory is `p2pool-v2` while the GitHub org and repo are
both `p2poolv2`. Use the GitHub URLs above in page links, never the old
`pool2win/p2pool-v2` paths that some wiki pages still carry.

Other useful links: the project board at
https://github.com/orgs/p2poolv2/projects/1, the Matrix chat at
https://matrix.to/#/#p2poolv2:matrix.org, and the X account at
https://x.com/p2poolv2. The GitHub and X links appear as icons in both
the top bar and the footer.

## Read before writing copy

Never describe the protocol from memory or from filenames. The claims on
this page are sourced, and they go stale when the protocol moves.

In the wiki (`../p2pool-v2.wiki`):

- `System Design.md` -- the two components and what each does
- `P2Pool vs P2Poolv2.md` -- uncles, vardiff, tradable share outputs
- `Comparison with DATUM and SV2.md` -- the share accounting argument
- `Shares For Small Miners.md` -- the small miner probability and its assumptions
- `Payout mechanism - Trading Shares For Bitcoin.md` -- coinbase, market makers, trading window
- `drafts/hashrate-market-overview.md` -- the instrument, what a share is worth, settlement rails, market structure. Newer than the page above and disagrees with it about the window; see the open question at the end of this file
- `Sharechain Design.md` -- share structure and replication. Its compact-block bandwidth and storage numbers are not on the page; see the note below
- `Miner Share Address.md` -- the two addresses and the `p2p=` password field
- `Status.md` -- what actually runs today

In the source repo (`../p2pool-v2`):

- `CLAUDE.md` -- project conventions, crate map, domain glossary
- `docs/architecture/` -- share pipeline, async flow, store schema, pruning, metrics
- `docs/atomic-swap/` -- the Lightning swap walkthrough and the HTLC scripts
- `Cargo.toml` -- the version number quoted in the hero caption
- `config-testnet4.toml` and friends -- stratum port and address prefixes

## Things on the page that must stay true

- The page leads with three properties, and the hero states each as a
  positive assertion rather than as something the pool lacks. Strunk
  Rule 11: "no operator approves you" is evasion, "the pool is
  permissionless" is an assertion. The vocabulary, which should not drift
  back to the negative form:

  | property | say | not |
  |---|---|---|
  | permission | permissionless; it checks your work, not your identity | no account, no operator to approve you |
  | accounting | auditable; verifiable; every node replays every share | no company keeps the books |
  | custody | self-custodial; it arrives on a key you hold | the pool never touches it, no custodian |

  The headline names the three properties directly. That is the hero and
  it should stay the hero.
  Below the headline, three boxes expand the three properties, one each,
  in the same order. They are a single bordered frame split by hairlines
  (the swap-strip construction, class `.prop`), not free-floating cards,
  and they carry no numbers because the three are parallel, not a
  sequence. Each box's orange label is a `.sub`. Keep box order and the
  headline order in step.
- The market is the second idea, not the first. The work you mine is an
  asset you sell for bitcoin, and that market is P2Poolv2's answer to
  payout scalability: firmware caps how many outputs a coinbase can
  carry, which is the ceiling that stopped the original P2Pool, and
  paying a handful of market makers instead of every miner is what gets
  past it. That argument lives in "How a share becomes bitcoin" and the
  hero links to it. Keep it there rather than folding it back into the
  hero, and do not lose it.
- Payout runs on two tracks and the distinction is load-bearing. The top N
  miners for a block take an output in that block's coinbase. Everyone
  else sells their share-chain outputs to a market maker. Never write that
  miners are paid from the coinbase without saying which of the two you
  mean; an earlier draft did and it was wrong.
- The argument against DATUM and Stratum V2 is about censorship, not just
  decentralisation for its own sake. Template freedom is worth only as
  much as the pool's willingness to pay for its use: an operator is an
  organisation with a jurisdiction and can be pressured, and it does not
  have to reject a template to punish one, it just stops counting the
  miner's shares. Keep that causal chain intact; a version that only says
  "accounting is centralised" loses the point. Source: the opening of
  `Comparison with DATUM and SV2.md` in the wiki.
- Settlement has two rails and both get named: Lightning and Ark. The
  share-chain side is an HTLC either way; the bitcoin side is a BOLT11 or
  BOLT12 invoice, or an Ark VHTLC, under the same payment hash. Ark is the
  better rail for a small miner because receiving over Lightning requires
  inbound liquidity. Adaptor signatures remain a third option. Source:
  `drafts/hashrate-market-overview.md` in the wiki, section 6. Do not let
  the page drift back to Lightning-only; `docs/atomic-swap/` in the node
  repo only documents the Lightning flow, which is why it reads that way.
- The market is designed, not shipped. The chain, the accounting and the
  coinbase payouts run on testnet4; the HTLC scripts and the Lightning
  swap are under "Being built". The hero caption says so and should keep
  saying so.
- Compact blocks are **not** mentioned, deliberately. The wiki describes
  shares as compact blocks of roughly 30 kB, giving about 1.3 Mbps of
  relay instead of 176 Mbps, and the page used to carry that as a fourth
  answer in "What we changed since the first P2Pool". It came out
  because it may not be implemented. Do not restore it from the wiki
  without asking, however good the numbers look.
- **The ask is always "run a node", never "hash on ours".** The primary
  CTA in the hero and the top bar both point at `#run`. The pool is only
  as decentralised as the number of nodes in it, so a page that recruits
  hashrate onto one public node argues against the project it is selling:
  the node operator gets the say over whether a miner's work reaches the
  chain, even though custody of the payout is unaffected. That reasoning
  is on the page in the first bullet of "If you mine", and it should stay
  there.
- The running testnet4 pool is shown as **proof, not as the destination**.
  The top bar carries a live dot linking to it, the hero offers "See the
  live chain" as the secondary action, and the status section links it
  along with the Grafana metrics. It is somewhere to look before you run
  your own, and for a first try; it is not what the page asks you to do.
- If a host goes away, fix or remove the claim with it rather than
  leaving a dead link under a sentence asserting the thing is running.
- That P2Poolv2 runs on testnet4 and signet, and is not ready for mainnet
  hashrate. The "Where the code is today" section says so plainly. Do not
  soften it without being asked.
- The numbers in "If you mine": 99.9999996% with uncles, 99.2% without,
  two S19 Pros, and the assumptions they rest on (P2Poolv2 at 1% of
  network hashrate, a share every ten seconds).
- The two miner addresses are separate things. `miner_bitcoin_address`
  takes the PPLNS payout from a found block's coinbase.
  `miner_address` is the bech32m P2Poolv2 address that owns the share
  coinbase and must be spend-capable. Never derive one from the other or
  substitute one for the other.

## Brand

Everything comes from `../logos/p2poolv2-brand-pack`. Its `README.txt`
carries the palette and the font list.

The pack specifies four colours, and they are the only colours on this
site:

| Pack name | Value | Where it goes |
|---|---|---|
| Primary, Bitcoin Orange | `#F7931A` | all structure: rules, borders, markers, the chain figure, the logo |
| Black | `#0B0B0C` | the page |
| Near Black | `#121212` | raised panels, terminal blocks |
| White | `#FFFFFF` | headings, prose, and the second mark |

There is no third accent. Orange and white are the only two marks, and
each is used where it has contrast to spare:

- Orange carries structure, so inside the orange chain figure the share
  that also cleared the bitcoin network target is **white**.
- Prose is white, so inside it the small miner probability is **orange**.

That is the whole system. An earlier draft used red with a gold accent;
both are gone. The reasons are worth keeping, because they are the traps:

- Orange against gold is **1.28:1**. A gold marker sitting among orange
  shares is invisible to everyone, not just to colour-blind readers. Do
  not reintroduce gold as a second mark.
- White on orange is **2.30:1** and fails AA. Black on orange is 8.57:1.
  Anything sitting on a filled orange surface takes `var(--black)`, which
  is why the primary button has black text.
- Orange on black is **8.57:1** and passes AA for body text. The old red
  was 3.95:1 and did not.

Rules that a change should not break:

- Every colour literal in `styles.css` lives in the `:root` block at the
  top of the file, and nowhere else. `index.html` contains no colours at
  all: the inline SVGs inherit `fill` from CSS.
- The palette reaches Bootstrap through Bootstrap's own variables, set
  once in that same `:root` block: `--bs-body-bg`, `--bs-primary`,
  `--bs-primary-rgb`, `--bs-border-color`, `--bs-link-color`,
  `--bs-secondary-color` and the rest. Theme a component by setting its
  `--bs-btn-*` or `--bs-table-*` variables, not by overriding Bootstrap's
  selectors.
- `--bs-border-radius` and its variants are all `0`. That single
  declaration is most of what keeps the page from looking like a stock
  Bootstrap page, along with using hairline-separated rows rather than a
  stack of `card`s.
- Bootstrap's default link and button colours do not know about the
  orange contrast rule. `.btn-primary`, `.btn-outline-primary` and
  anything else that fills with orange set `--bs-btn-color` and
  `--bs-btn-hover-color` to black. Check any new orange-filled control.
- Do not introduce a fifth colour. Where a shade is needed, use one of the
  four at reduced opacity and add it to `:root` as a named token
  (`--orange-26`, `--white-56`). The pack does the same thing for the faint
  mesh lines in the logo, which are the primary orange at about 35%.
- Type is JetBrains Mono for headings, the wordmark and data; Inter for
  prose. Both are in the pack's suggested font list.

To audit, `grep -oE '#[0-9a-fA-F]{3,8}|rgba?\([^)]*\)' styles.css` should
return nothing outside `:root`, and the same grep over `index.html` should
return nothing at all.

Assets in `assets/`:

- `logo.png` -- the lockup, used in **both** the top bar (38px tall) and
  the footer (200px wide). It is the pack's
  `p2poolv2-logo-text-network-bitcoin-orange-transparent`, cropped to the
  artwork and quantised to 64 colours, 760x380 and 28 KB. Cropping to the
  artwork is what makes the small size work: the shipped file has a wide
  transparent margin, and the wordmark is only 29% of the full artwork's
  height.
- `logo.svg` -- the same lockup as vector, copied unchanged from the
  pack, kept as the master for print and reuse. The page does **not** use
  it: the pack sets the wordmark in `<text>`, and an SVG loaded through
  an `img` has no webfont, so it substitutes whatever sans the machine
  happens to have. It matches on Linux by luck, because the artwork was
  rendered from DejaVu, and would not on macOS or Windows.
- Do not use `p2poolv2-logo-text-network-bitcoin-orange.png` or the
  matching `.svg` on a dark page. Both bake in a `#121212` background
  rectangle, which shows as a lighter box against the `#0B0B0C` page.
  The `-transparent` variants are the ones to take.
- `mark.svg` -- the pack's four-node mark in Bitcoin Orange, used as the
  favicon. The top bar used to use it alongside a wordmark typed in
  JetBrains Mono; it now carries the real lockup instead, so this file is
  only the favicon.

The pack's `README.txt` lists the defects still open in the pack itself:
a dead red stylesheet and stray `fill:#00001a` inside the orange logo, two
SVGs that are not well-formed XML, and a `palette.svg` still drawn in red.
None of them affect this site. The geometry in `assets/logo.svg` was
extracted from the outlines file and repaired.

## Regenerating the vendored assets

**Bootstrap.** Download the release and check it against the hash
published for the CDN copy, which is how you know the file is the real
one:

```
curl -o assets/bootstrap.min.css \
  https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css
openssl dgst -sha384 -binary assets/bootstrap.min.css | openssl base64 -A
# expect QWTKZyjpPEjISv5WaRU9OFeRpok6YctnYmDr5pNlyT2bRjXh0JMhjY6hW+ALEwIH
```

**Fonts.** `assets/fonts.css` is generated and should not be hand-edited.
Fetch the Google Fonts stylesheet with a modern browser User-Agent, or it
serves legacy formats instead of woff2:

```
curl -A 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0 Safari/537.36' \
  'https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600&family=JetBrains+Mono:wght@400;500;700;800&display=swap'
```

Two things about the result are worth knowing before you regenerate it:

- It returns 45 `@font-face` blocks across seven subsets. Only `latin`
  and `latin-ext` are vendored, because the page is ASCII. Pulling all
  seven would triple the weight for nothing.
- Both families are **variable fonts**, so Google serves the same file
  for every weight you ask for. Naively downloading per weight gives you
  four identical copies of JetBrains Mono. There is one file per family
  per subset, and the `font-weight: 400 800` range in the `@font-face`
  is what lets the browser interpolate. If you add a weight outside a
  declared range, widen the range rather than adding a file.

Licences live in `assets/fonts/`. Inter and JetBrains Mono are both SIL
OFL 1.1, which permits redistribution; keep the licence files next to the
fonts.

## The logo at small sizes

The lockup is height-inefficient: the wordmark occupies 29% of the
artwork's height and the rest is mesh, and "v2" sits on a dropped
baseline below and right of "p2pool". Both are composition decisions in
the artwork, so neither can be fixed in CSS. In practice this means:

- Crop to the artwork before using it small. `assets/logo.png` already
  is; the files in the pack are not.
- 38px is the floor that still reads in the top bar. Below that the "v2"
  closes up and the mesh turns to speckle.
- Two redrawn alternatives were mocked up and not adopted: a single
  scalable lockup with the nodes bound to the letterforms, and a family
  of primary plus compact lockup plus icon. If the logo is ever revisited,
  those are the two shapes the problem has, and the wordmark should be
  outlined rather than left as `<text>`, which is what the pack's own
  README advises.

## Bootstrap gotchas found here

- **A `.row` that carries a border needs `gx-0`.** Bootstrap rows use
  negative horizontal margins for their gutters, so a `border-top` on a
  row draws wider than the container and the hairline sticks out past the
  text. The definition rows and the stat block use `gx-0` with `gy-*` for
  vertical spacing, and `pe-lg-5` on the first column for the space that
  the gutter would have provided.
- **Utilities carry `!important`.** `class="band pt-5"` will not give you
  `band`'s padding on top; the utility wins. Use one or the other.
- **`.container` is capped at 1120px here**, narrower than Bootstrap's
  default, so the measure stays readable on a wide screen.

## Deploying

The page is served at `https://p2poolv2.org/`, which is set in the
`canonical` link and in `og:url`. Deploy with the Ansible playbook, which
the README documents:

```
cd ansible
ansible-playbook deploy.yml --check   # dry run
ansible-playbook deploy.yml
```

The playbook pulls `main` into `~/website` on the host and rsyncs
`index.html`, `styles.css` and `assets/` into `/var/www/p2poolv2.org`.
It deploys what is on GitHub, so push first. It then fetches the page and
the stylesheet over the public URL, compares each with the file on the
host, and checks that `/.git/HEAD` returns 404.

- **The webroot takes an allowlist.** Anything outside it is deleted,
  which keeps `.git` and the Markdown files off the web. An earlier
  manual `cp` of the whole checkout served `/.git/` publicly. If the page
  gains a top-level file, such as `robots.txt`, add an `--include` for it
  in `deploy.yml` or the deploy will delete it.
- **Do not copy the checkout by hand.** Use the playbook, for the same
  reason.
- **The playbook does not reload nginx.** nginx reads static files from
  disk on each request, so new content is live once copied. Only a config
  change needs a reload, and this repository owns no nginx config.
- **The nginx server block is a hand edit.** The `p2poolv2.org` block
  lives in `/etc/nginx/sites-available/testnet4.conf` on the host, with
  `root /var/www/p2poolv2.org`. The node repo's Ansible role
  (`../p2pool-v2/ansible/roles/nginx_p2poolv2`) still templates
  `p2poolv2.org` as a redirect to testnet4. Running that role again would
  take the landing page off the apex until its template is changed to
  match.

The social card at `assets/og.png` is generated, 1200x630, and shows the
headline. If the headline changes, regenerate it: build a 1200x630 page
using `assets/logo.png` and the headline set in `assets/fonts.css`,
screenshot it headless, and save it over that file.

## Working on it

```
python3 -m http.server -d . 8000
```

To check a change without a browser window:

```
chromium --headless --disable-gpu --hide-scrollbars \
  --force-prefers-reduced-motion --window-size=1440,7200 \
  --screenshot=/tmp/page.png --virtual-time-budget=6000 \
  file://$PWD/index.html
```

Look at the screenshot. Check 390px wide as well as desktop.

## Writing

The copy follows Strunk, *The Elements of Style*
(https://www.gutenberg.org/files/37134/37134-h/37134-h.htm). The rules
that actually bite on this page, in the order they get broken:

- **Omit needless words (13).** Every word must tell. "This is the payout
  scalability answer, so it is worth being precise about it" says nothing;
  "Payout scalability is the problem this solves" says the same thing and
  starts the paragraph. Cut "really", "very", "actually", "simply", "the
  fact that", "in order to", and any sentence that only announces what the
  next sentence will do.
- **Avoid a succession of loose sentences (14).** The natural draft here
  is three clauses strung with "and", because the subject invites it. Two
  sentences almost always read better than one with two "and"s in it.
- **Use the active voice (10).** "The PPLNS window is recomputed locally"
  became "Each node recomputes the PPLNS window". Name the actor: a node,
  a miner, a market maker, the pool.
- **Put statements in positive form (11).** The trap on this page is
  that every selling point is an absence: no operator, no account, no
  custodian, no permission. Stacking those reads as evasion. Name the
  property instead, which is what "permissionless", "auditable" and
  "self-custodial" are for; the table under "Things on the page that
  must stay true" has the mapping. Keep the negative where the denial is
  the point, as in "not ready for your mainnet hashrate", and in the
  section on pressure, where the antithesis is the argument.
- **Definite, specific, concrete language (12).** "That output is the
  thing you can sell" is vague; say what it is and what happens to it.
  Numbers beat adjectives: 8.57:1, 99.9999996%, three uncles, ten seconds.
- **Place the emphatic words at the end (18).** The paragraph on pressure
  ends on "It stops counting your shares" because that is the point of it.
- **Express co-ordinate ideas in similar form (15).** The chain figure's
  three legend entries all read "A thing: what it is". Lists of parallel
  items get parallel grammar.
- **Begin each paragraph with a topic sentence (9).** One topic to a
  paragraph.
- **Serial comma (2).** "bitcoind, the node, and a Lightning node."
- **Do not join independent clauses by a comma (5).** "A pool operator is
  a company in a jurisdiction, and companies can be leaned on", not a
  comma splice.

None of this licenses shortening the page by dropping substance. Strunk's
own gloss: "This requires not that the writer make all his sentences
short, or that he avoid all detail, but that every word tell."

## House rules

- The page is built on **Bootstrap 5.3.3**, CSS and the JS bundle, both
  vendored at `assets/bootstrap.min.css` and
  `assets/bootstrap.bundle.min.js`. There is no build step and no npm.
  The JS is there for one thing, the navbar collapse; if something else
  needs JavaScript, ask first.
- **The nav breakpoint is `lg` (992px), and that number was measured.**
  The full bar, four links plus the live indicator, the GitHub and X
  icons and the button, needs about 890px with the logo. It fits inline
  at 992px and wraps at 768px, which is why the breakpoint moved up from
  `md`. If a link is added or renamed, re-check 992px before assuming it
  still fits.
- Below 992px the links, the live indicator and the CTA all move into a
  `navbar-collapse` behind a toggler, so everything in the nav stays
  reachable on a phone. The toggler icon is a background SVG in
  `--bs-navbar-toggler-icon-bg`, so recolouring it means replacing that
  data URI, not setting a `fill`.
- **Nothing loads from a third party.** No CDN, no Google Fonts, no
  analytics. Adding an external `<link>`, `<script>` or `@import` is a
  change of policy, not a detail: it puts visitor IPs in someone else's
  logs and breaks the page wherever that host is blocked, which for a
  mining audience on isolated networks is a real case. To check, grep the
  page for `//` in a URL, or render it with DNS blocked:

  ```
  chromium --headless --host-resolver-rules="MAP * ~NOTFOUND, EXCLUDE 127.0.0.1" \
    --screenshot=/tmp/offline.png http://127.0.0.1:8000/
  ```
- Reach for a Bootstrap utility before writing CSS. Grid (`row`,
  `col-lg-7`), spacing (`py-4`, `mb-0`, `gap-3`), flex
  (`d-flex flex-wrap justify-content-between`), `table`, `btn`,
  `list-unstyled` and `visually-hidden-focusable` are all in use. Add a
  rule to `styles.css` only for something Bootstrap has no equivalent
  for.
- ASCII only in markup, styles and copy, matching the node repo's
  convention. Use HTML entities for typographic characters.
- One animation on the page: the hero chain figure builds once on load. It
  holds still under `prefers-reduced-motion`. Do not add hover transitions
  on cards or scroll reveals on sections.
- Left-aligned throughout. The page should read like a document, not a
  pitch deck.
- Keep the quality floor: visible keyboard focus, a skip link, real
  `<table>` markup for tabular data, figure captions that carry
  information, and no horizontal scroll at phone width.
- Copy is plain and active. Say what a thing does rather than selling it.
  Where the honest answer is "not yet", say that.

## Open question

The page describes the trading window as maturity after about a day of
share blocks, closing when the PPLNS window moves past that depth. That
comes from `Payout mechanism - Trading Shares For Bitcoin.md`.

The newer `drafts/hashrate-market-overview.md` describes it differently:
the PPLNS window is a sliding window of 2,016 bitcoin blocks, a share
mined at height `x` expires at `x + 2016`, expiry is continuous rather
than batched, and the earlier `8 x D` work cap has been dropped on
purpose. It also prices shares in sats per EH and sets out a two-sided
order book in 144-block buckets.

These are different models and the draft looks like the current thinking.
The page has not been updated to it because the file is in `drafts/`. Ask
before rewriting the trading window section.

## Writing Style

Remain 100% true to elements of style book by william strunk jr. You
can find a copy here:
https://www.gutenberg.org/files/37134/37134-h/37134-h.htm Ingest the
style guidelines.
