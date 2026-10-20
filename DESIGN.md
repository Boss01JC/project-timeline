[DESIGN.md](https://github.com/user-attachments/files/33711783/DESIGN.md)
# JIAN CHA Franchise Ops — Design guide

One page of rules so new screens look like old ones. The tokens below exist as CSS variables
(`:root` in `index.html`); use them instead of new numbers.

## Principles
1. **One obvious next step per screen.** Exactly one primary (gold) button per screen region. Export / print / CSV are secondary (`btn-ghost`).
2. **Readable first.** Text meets WCAG 2.2 AA (4.5:1; 3:1 for large text). Never put brand gold or orange *as text* on a light surface — use the `-text` colours.
3. **Touch-friendly.** Controls are at least 40px tall (44px on touch). Nothing interactive is smaller than 24×24px; icon-only buttons need an `aria-label`.
4. **Light theme only.** The theme is deliberately locked to light (`data-theme="light"`). Do not add dark mode without a decision.
5. **Thai first.** UI labels in Thai; keep an English term in brackets only where staff use it (Timeline, Files, Finance).

## Colour
| Token | Value | Use |
|---|---|---|
| `--navy` | `#1B3A6B` | headings accents, secondary-strong buttons, selected state |
| `--navy-dark` | `#0F2240` | top bar, hero, table headers |
| `--gold` | `#C9A800` | **fill only** — primary button background, highlights. Text on gold is `#12200F` (use `inkFor()`) |
| `--gold-text` | `#7A6200` | gold used as text on light surfaces |
| `--text` / `--text-2` / `--text-3` | `#1A1714` / `#6A6056` / `#6B6259` | body / secondary / hints (all ≥ 4.6:1 on `--bg`, `--surface`, `--surface-2`) |
| `--green-text` | `#1F7A44` | done / ok text |
| `--orange-text` | `#A8420A` | warning / "waiting" text |
| `--red-text` | `#B42318` | error / overdue text |
| `--sky-dark` | `#0B6E8F` | info / links-as-state |
| status text | `STATUS_TEXT` map in JS | Planning `#0B6E8F`, Active `#1F7A44`, Completed `#5F574F`, On Hold `#A8420A` |

Branch / department / role colours are user-chosen; text on them is picked automatically with `inkFor(bg)` (white or `#12200F`, whichever reads better). Never hard-code white text on a data colour.

## Type (rem — honours the browser/OS text-size setting)
`--fs-xs .75rem (12) · --fs-sm .875rem (14) · --fs-md 1rem (16) · --fs-lg 1.25rem (20) · --fs-xl 1.5rem (24) · --fs-2xl 2rem (32)`
- Weights 400 / 700 / 800. Body line-height 1.55 (Thai needs the room).
- Body copy ≥ 12px; labels, chips, badges ≥ 11px. Only the dense Gantt grid (day headers, bar labels) may go lower.
- Section sub-headings: 16px / 800 / navy (not small grey caps).

## Space, shape, depth
- Spacing grid 4px: `--sp-1…6 = 4 · 8 · 12 · 16 · 24 · 32`.
- Radius: controls `--r-ctl` 8px · cards `--r-card` 14px · chips/pills `--r-pill`.
- Cards share one shadow: `--shadow-sm`; hero blocks use `--shadow-lift`.
- Control height `--ctl-h`: 40px (44px when `pointer: coarse`).

## Components
- **Buttons:** `btn-gold` primary · `btn-navy` strong secondary · `btn-ghost` / `btn-ghost2` tertiary & exports · `btn-danger` / `dlg-danger` destructive. Hover, pressed (`brightness .9`), focus-visible (3px gold ring) and disabled states are mandatory.
- **Inputs:** `finput` / `sf-input` / `cf-input` (min-height `--ctl-h`). Errors appear **under the field** with `fieldError(el,msg)` (red border, `aria-invalid`, `role=alert`) — a toast is not enough.
- **Dialogs:** open with `openP(id)` (static popups) or `_prompt/_confirm/_linkPrompt`. They manage focus, Tab-trap, Esc (top layer only), scroll lock and focus return. Confirm buttons say what they do (`{yes:'ออกจากระบบ'}`); red "delete" only for deletes.
- **Toasts:** `_toast(msg,'ok'|'warn'|'err')` — stack in one live region, sit above the phone tab bar; errors stay longer.
- **Clickable non-buttons:** prefer `<button>`. If a `div/span/td` must be clickable, give it `onclick` — the app adds `role=button`, `tabindex` and Enter/Space automatically.
- **Wide tables:** wrap in a container listed in `HS_SEL` so edges fade and phones get a "scroll →" caption.

## Safety rules for markup
- Never place a name, file name or free text inside an inline handler. Handlers receive **ids/codes only** (sanitised by `_sanitizeShop`); look names up from data.
- Escape every user string with `esc()`; links go through `safeHref()` (http/https only) with `rel="noopener noreferrer"`.
- Colours from data pass through `_safeColor()`.

## Language (TH / EN)
- Screens are written in Thai. The **EN/ไทย** button (top bar and the link-needed page) swaps text for English through the dictionary in the `jc-i18n` script at the end of `index.html` (`DICT` = Thai phrase → English; `PATTERNS` = whole messages that carry a value, e.g. a name or a count).
- **New Thai text needs a dictionary entry**, or it stays Thai in English mode. Keep each phrase as one literal (not glued from pieces) so it can be looked up whole.
- Names, notes, comments and file names that people type are never translated unless the whole text is a built-in phrase; input values are never touched. Mark anything that must never change with `translate="no"`.
- Dates requested with `'th-TH'` show as normal English dates (e.g. 19 Oct 2026) in English mode.

## Checklist before shipping a screen
- [ ] One primary button · all text ≥ AA contrast · no text below the size floor
- [ ] Works at 320px wide with no sideways page scroll; tables scroll inside their own box
- [ ] Every control reachable and operable by keyboard; focus visible; dialog focus returns
- [ ] Empty, loading and error states each say what to do next
- [ ] Title/`<h1>` reflect the page (`setPageTitle()`)
- [ ] Switch to EN: no Thai left on the screen except typed data
