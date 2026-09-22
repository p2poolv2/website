# AGENTS.md

Guidance for AI coding agents working on the P2Poolv2 website.

This directory is the public landing page for P2Poolv2. It is plain HTML
and CSS with no build step and no dependencies beyond two webfonts from
Google Fonts. It is a separate project from the node source, and is not
part of the `p2poolv2` git repository.

## Where everything is

Local checkouts, all siblings of this directory under
`/home/kulpreet/projects/p2poolv2/`:

| What | Path | Online |
|---|---|---|
| Brand pack | `../logos/p2poolv2-brand-pack` | -- |
| Other logos | `../logos` | -- |
| Wiki | `../p2pool-v2.wiki` | https://github.com/p2poolv2/p2poolv2/wiki |
| Node source | `../p2pool-v2` | https://github.com/p2poolv2/p2poolv2 |

Note the local directory is `p2pool-v2` while the GitHub org and repo are
both `p2poolv2`. Use the GitHub URLs above in page links, never the old
`pool2win/p2pool-v2` paths that some wiki pages still carry.

Other useful links: the project board at
https://github.com/orgs/p2poolv2/projects/1 and the Matrix chat at
https://matrix.to/#/#p2poolv2:matrix.org.

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
- `Sharechain Design.md` -- compact blocks, bandwidth and storage numbers
- `Miner Share Address.md` -- the two addresses and the `p2p=` password field
- `Status.md` -- what actually runs today

In the source repo (`../p2pool-v2`):

- `CLAUDE.md` -- project conventions, crate map, domain glossary
- `docs/architecture/` -- share pipeline, async flow, store schema, pruning, metrics
- `docs/atomic-swap/` -- the Lightning swap walkthrough and the HTLC scripts
- `Cargo.toml` -- the version number quoted in the hero caption
- `config-testnet4.toml` and friends -- stratum port and address prefixes

## Things on the page that must stay true

- The page leads with the market: the work you mine is an asset you sell
  for bitcoin, and that market is P2Poolv2's answer to payout
  scalability. Firmware caps how many outputs a coinbase can carry, which
  is the ceiling that stopped the original P2Pool; paying a handful of
  market makers instead of every miner is what gets past it. Do not demote
  this to a feature further down the page.
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
- The version in the hero caption, currently 0.15.3.
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

- `logo.svg` -- the pack's full lockup, repaired, transparent background,
  Bitcoin Orange, wordmark set in JetBrains Mono. Inlined into the footer
  so it picks up the page webfont; the standalone file is for reuse
  elsewhere.
- `mark.svg` -- the pack's four-node mark, used as the favicon and inlined
  in the top bar.

The pack's `README.txt` lists the defects still open in the pack itself:
a dead red stylesheet and stray `fill:#00001a` inside the orange logo, two
SVGs that are not well-formed XML, and a `palette.svg` still drawn in red.
None of them affect this site. The geometry in `assets/logo.svg` was
extracted from the outlines file and repaired.

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
- **Put statements in positive form (11).** "The money never sits in an
  account somebody else controls" became "the payment goes straight to a
  key you hold". Keep the negative only where the denial is the point, as
  in "not ready for your mainnet hashrate".
- **Definite, specific, concrete language (12).** "That output is the
  thing you can sell" is vague; say what it is and what happens to it.
  Numbers beat adjectives: 8.57:1, 30 kB, 1.3 Mbps, three uncles.
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

- The page is built on **Bootstrap 5.3.3 CSS**, loaded from jsDelivr with
  an SRI hash. There is no build step, no npm, and no Bootstrap
  JavaScript: the top bar does not collapse, it hides its links with
  `d-none d-lg-inline` instead, so the page stays CSS-only. If something
  needs JavaScript, ask first.
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
