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

## House rules

- Plain HTML and CSS. No framework, no bundler, no npm. If something needs
  JavaScript, ask first.
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
