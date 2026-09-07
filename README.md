# howtoposttrain.com

Standalone static site for **How to Post-Train** — Auriel's RL Pet Peeves series.

Split out of [`aurielws.github.io`](https://github.com/Aurielws/aurielws.github.io), which
stays as the personal site (about / coaching / advising / community / contact).

## Layout

```
CNAME                          howtoposttrain.com
.nojekyll                      plain static site, skip Jekyll processing
index.html                     series index / home
styles.css                     the LessWrong-styled theme (see Theme below)
404.html
robots.txt
sitemap.xml
rl-pet-peeves-part-1/          01 · You've Never Spent Real Time with Your Model  (+ images)
rl-pet-peeves-rubric/          02 · Your Rubric Was Written by Someone Who Has Never Done the Job
rl-pet-peeves-economic/        03 · Your Tasks Are Not Grounded in Economic Reality
rl-pet-peeves-simulation/      04 · Your Data Screams "This Is a Simulation"
glossary/                      RL glossary
```

Post 5 (*Environment Quality*) is listed as "Coming" on the index and has no page yet.
Drafts still live in `aurielws.github.io/writing-drafts/` and were deliberately not copied here.

## Theme

The site is styled after [LessWrong](https://www.lesswrong.com/wikitags/all). Tokens are
taken from LessWrong's own open-source theme (ForumMagnum,
`packages/lesswrong/themes/defaultPalette.ts` and `createThemeDefaults.ts`) rather than
eyeballed:

| Token | Value | LessWrong source |
|---|---|---|
| page background | `#f8f4ee` | `background.default` |
| panel background | `#fff` | `panelBackground.default` |
| row hover | `#f0ebe6` | `background.hover` |
| body text | `rgba(0,0,0,.87)` | `text.normal` |
| secondary text | `rgba(0,0,0,.54)` | `text.secondary` |
| link | `#327E09` | `link.color` |
| visited link | `#798754` | `link.visited` |
| primary / accent | `#5f9b65` | `primary.main` |
| border | `rgba(0,0,0,.2)` | `border.normal` |
| row separator | `2px rgba(0,0,0,.05)` | `border.itemSeparatorBottom` |

Fonts are LessWrong's stacks verbatim — serif `warnock-pro, Palatino, "Palatino Linotype",
"Palatino LT STD", "Book Antiqua", Georgia, serif` for body and headings, and
`Calibri, gill-sans-nova, "Gill Sans", …` for UI chrome. These are system fonts, so the
site loads **no webfonts at all**; most visitors land on Palatino or Georgia, which is
what LessWrong itself renders for them.

`styles.css` is linked **last** in every page's `<head>`, after that page's own inline
`<style>`. Two consequences worth knowing before editing:

- The posts still carry their original inline CSS. `styles.css` re-skins them by
  redefining the legacy `--cream` / `--ink` / `--accent` / `--s1..--s4` custom properties
  and by re-declaring the handful of selectors that hardcoded the old display font. It
  wins on source order, so no `!important` is needed — but **the link must stay last** or
  the posts revert to the old palette.
- `--s2` and `--s4` deliberately stay a warning amber and an approving green. The
  comparison tables in the rubric post rely on that contrast to distinguish the bad
  example from the good one.

Content column widths (`760px` for `.container`, `800px` for `.post-content`) match what
each post was originally authored against, so the inline diagrams still fit. The
five-stage `.pf-flow` diagram in post 1 scrolls horizontally by the author's own design.

## Preview locally

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

Root-relative links (`/glossary/`, `/styles.css`) resolve correctly under a server rooted
at this directory. Opening the files directly with `file://` will not work.

## Deploying

### 1. Turn on GitHub Pages

Repo **Settings → Pages**:

- Source: *Deploy from a branch*
- Branch: `main`, folder `/ (root)`
- Custom domain: `howtoposttrain.com` (the `CNAME` file already sets this)
- Tick **Enforce HTTPS** once the certificate is issued — this can take up to ~24h
  after DNS resolves, and the checkbox stays greyed out until then

The repo must be **public** for Pages on a free plan.

### 2. DNS at the registrar

The apex domain needs four A records and four AAAA records; `www` needs a CNAME.
**Delete the registrar's default parked `@` A record and `www` CNAME first** — leaving
them in place is the usual reason the domain keeps showing a parking page.

| Type  | Name  | Value               |
|-------|-------|---------------------|
| A     | `@`   | `185.199.108.153`   |
| A     | `@`   | `185.199.109.153`   |
| A     | `@`   | `185.199.110.153`   |
| A     | `@`   | `185.199.111.153`   |
| AAAA  | `@`   | `2606:50c0:8000::153` |
| AAAA  | `@`   | `2606:50c0:8001::153` |
| AAAA  | `@`   | `2606:50c0:8002::153` |
| AAAA  | `@`   | `2606:50c0:8003::153` |
| CNAME | `www` | `aurielws.github.io` |

The `www` CNAME points at the **GitHub Pages user host**, `aurielws.github.io` — not at
this repo, and not at `howtoposttrain.com`.

Verify propagation before expecting the site to load:

```bash
dig +short howtoposttrain.com
dig +short www.howtoposttrain.com
```

### 3. Cross-link from the personal site

`aurielws.github.io/writing.html` should point its series entries at `https://howtoposttrain.com/`
so the old URLs keep working as an entry point.

## Notes

- Links back to the personal site (`About Auriel`, `Work with me`, the footer byline) are
  absolute `https://aurielws.github.io/...` URLs and keep working from the new domain.
- Every page carries a `<link rel="canonical">` pointing at its `howtoposttrain.com` URL,
  so search engines attribute the content to this domain rather than the old paths.
- GitHub Pages allows one custom domain per Pages site. That is why this is a separate repo
  rather than a subdirectory of the personal site — setting a custom domain on
  `aurielws.github.io` would redirect the entire personal site to it.
