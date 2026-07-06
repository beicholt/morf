# MORF — Project Context (for Claude Projects)

> **Renamed 2026-06-15:** BLURT! → **MORF**. Repo renamed `blurt` → `morf`,
> all in-game strings / localStorage keys / `PLAY_URL` updated, README swept,
> OG/Twitter tags + PWA manifest + GoatCounter analytics added. See the
> "Naming status" and "Immediate next steps" sections below for current state.

A free, single-file, zero-dependency web word game. Players turn a **start word**
into a **goal word**, changing **one letter per step**, where every intermediate
word must be a real English word. This is the classic **word ladder** puzzle
(Lewis Carroll's *Doublets*, 1879 — public domain), built with a modern
daily-puzzle / streak / share loop in the style of Wordle.

## Repo & deployment facts (verified)

- **Repo:** `beicholt/morf` on GitHub (was `Word-Game`, then `blurt`, renamed
  to `morf` on 2026-06-15). Old names `Word-Game` and `blurt` still redirect.
- **Dev branch:** `claude/wordle-replica-game-wv7rc0`, merged to `main` via PRs
  (#1 through #11 so far, all merged, working tree clean).
- **Everything lives in one `index.html`** (~78KB) + `README.md`. No build step,
  no dependencies. Run locally with `python3 -m http.server`.
- **GitHub Pages** auto-deploys `main`. Live URL is now
  `https://beicholt.github.io/morf/` (repo renamed to `morf`). The in-game
  `PLAY_URL` constant points to `https://morf.day` (the intended custom
  domain). README's "Play now" link uses the working Pages URL for now.
- **Domain `morf.day`** was purchased earlier ($10/yr) but **is NOT wired up**
  — user has paused on configuring it. Until the custom domain is pointed at
  Pages (repo Settings → Pages + DNS / `CNAME`), share text linking to
  `https://morf.day` will not resolve. Working link today is the Pages URL.

## Naming status — RESOLVED → MORF (executed 2026-06-15)

- Game was previously branded **"BLURT!"** everywhere; the full rename sweep to
  **MORF** has now been executed (title, header logo, help card, all UI
  strings, button labels like "MORF AGAIN", share text, README). The
  trademark-collision risk with the registered "BLURT!®" game drove the change.
- **"BLURT!®" is a registered trademark** for an existing word game (Keys
  Publishing Co. / Educational Insights / formerly PlayMonster) — same
  category, same name → real collision risk if this grows.
- **MORF** was proposed as the replacement:
  - Fits the mechanic ("morph" spelling twist), verbs naturally ("did you morf
    today?").
  - Trademark search found no MORF mark in games/software classes. Existing
    MORF marks are in apparel, alcohol, Christian publishing, and a MORFBOARD
    balance board (Class 28, unrelated). One soft collision: a small indie
    "Morf" game on Google Play (different genre, unregistered) — low risk for
    a free game.
  - `morf.day` purchased for $10/yr (`.game` TLD was $300/yr, rejected).
  - Verdict given: reasonably safe to launch for free as MORF; not
    "bulletproof" — recommend a free USPTO TESS search (classes 9 + 41) before
    registering the mark or spending more money.
- Other names considered:
  - **MOLT** — rejected by user (disliked it).
  - **NUDGE**, **SEGUE** — still on the table, not vetted (SEGUE has a
    spelling/homophone risk with "Segway").
  - **AMBLE**, **WAYWORD** — mentioned, not pursued (WAYWORD has an adjacent
    claim from *A Way with Words* radio show).
  - **WORM** — ruled out, category crowded with existing word-search/worm
    games.
- **Rename executed 2026-06-15.** Full sweep done: title, header logo, all UI
  strings, button labels, share text template, help card, README, `PLAY_URL`
  constant (→ `https://morf.day`), plus localStorage keys
  (`blurt-stats/daily/helped2` → `morf-*`). Note: renaming the localStorage
  keys resets existing players' streaks/stats (old `blurt-*` data is orphaned)
  — acceptable pre-launch.

## Game mechanics (as implemented today)

- **Word length:** 4 letters.
- **Goal:** transform start word → goal word, one letter changed per step,
  every intermediate step a real word.
- **Board layout:** top-down — start word at top, the active/editable row
  glows, goal word pinned at bottom with a dashed border.
- **Input:** native device keyboard via a hidden `<input id="kb">` (sentinel-
  character trick for cross-IME letter/backspace detection). Tapping any tile
  focuses it. No custom on-screen keyboard.
- **Color language** (consistent across board + share):
  - `--lock` **green** (`#27b364`) — letter matches the goal, only *after* a
    step is locked in (not live while typing).
  - `--changed` **purple** (`#8e5bff`) — the letter you just swapped this step.
  - **Red** (`#c0334a`) — the single letter that makes a submission invalid
    ("fumble").
- **"Perfect path" / par:** computed via BFS over a curated common-word graph,
  `COMMON` (2,386 words — frequency-ranked ∩ ENABLE dictionary, proper names
  purged). Displayed as "perfect is N" and used as the scoring benchmark.
- **Three word lists:**
  - `ANSWERS` (863 words) — pool that puzzle start/goal words are drawn from.
  - `GUESSES` (3,903 words) — the full **ENABLE** 4-letter word list. Any real
    word here is a valid step, even if not "common." `COMMON` words are unioned
    into the valid set too.
  - (Previously used a noisy `words_alpha` scrape that incorrectly validated
    fabrications like "bool" — replaced with ENABLE.)
- **Fumbles:** submitting an invalid word:
  - Stamps the row onto the board with only the wrong letter shown in red (no
    strikethrough, no full-row red).
  - **Costs a step** in the score (closes a brute-force/free-retry exploit).
  - Editing resumes from the last valid word.
  - Appears in the share trail as a single 🟥 at the correct position (e.g.,
    `🟥⬜⬜⬜`).
- **No undo button** (removed deliberately) — you can still retreat to a
  previous word by retyping it, but it costs a step like any other move. This
  was judged the correct "honest" penalty.
- **Ranks** (`RANKS` array in code, by `margin = steps - par`):
  - `margin < 0` → 🦅 **BEYOND PERFECT!** (found a shortcut shorter than the
    "perfect" graph path, via a rarer word)
  - `margin === 0` → 🔮 **PERFECT PATH!**
  - `margin === 1` → 🧠 **SO CLOSE!**
  - `margin === 2` → 🔥 **SOLID!**
  - `margin >= 3` → 😅 **GOT THERE!**

## Modes

- **DAILY** — deterministic seeded puzzle via `mulberry32(today * 7919 +
  12345)`, epoch 2026-01-01 = Daily #1, identical worldwide. One attempt per
  day; result (including fumble history) persisted to `localStorage`
  (`morf-daily`) and restored on revisit with a "Next word in Xh Ym"
  countdown. Tracks its own day-streak (`daily.streak`, increments only on
  consecutive-day wins).
- **ENDLESS** — new random puzzle immediately on completion. Streak
  (`stats.streak`) increments only on **perfect-or-better** results (margin
  ≤ 0), making the streak harder/spicier.
- Header shows streak (🔥), best, and solved counts — switches per active mode.
- Mode tabs (DAILY / ENDLESS) at the top of the board.

## Sharing (the core viral mechanic)

- **SHARE button** on every result card builds spoiler-free text, e.g.:
  ```
  MORF Daily #163 — VICE ➜ WERE
  3 steps · perfect is 3 🔮
  🟩⬜⬜🟩
  🟩⬜🟩🟩
  🟩🟩🟩🟩
  Find a shorter path.
  https://morf.day
  ```
  - Green = letters matching the goal at that step.
  - Purple = the swapped letter (when not also matching the goal).
  - Red = fumble position.
  - Always appends `PLAY_URL` (now `https://morf.day` — pending domain wiring).
  - Uses `navigator.share` (native share sheet) with `navigator.clipboard
    .writeText` fallback, then legacy `execCommand('copy')`.
- **Not yet built (Phase 1 GTM idea):** "challenge link" — encode a specific
  puzzle seed in the URL so a recipient plays the *exact same board* the
  sharer just solved. Flagged as the single highest-leverage virality
  improvement.

## UI/UX decision history (why things are the way they are)

1. **v1** was a literal Wordle clone (hidden word, 6 guesses, green/yellow/gray
   grid) — identified as a legal/creative problem, scrapped.
2. **v2 (the pivot):** rewrote the core mechanic to word-ladder/Doublets
   (public domain) — eliminated hidden-word guessing and the 6-row grid. This
   is the current mechanic.
3. Par/perfect-path was initially computed on the full noisy dictionary,
   producing unfair pars (paths requiring obscure words like "bhut"/"phut").
   Fixed by introducing the separate `COMMON` frequency-ranked graph.
4. **Board direction flip-flop:** top-down → tried "climbing" bottom-up (with
   rung/climb vocabulary) → reverted to top-down after play-testing felt
   awkward. Vocabulary reverted from climb/rung → **path/step** throughout
   (ranks, share text, help, tagline, README).
5. **On-screen keyboard removed**, replaced with hidden-input + native
   keyboard — freed vertical space, fixed "have to scroll to see board."
6. **Keyboard-open viewport bug:** an early fix over-corrected (board got
   `flex:1` creating dead space; "squeeze" compact mode triggered too early at
   <540px). Fixed: board sizes to content (`flex: 0 1 auto`), squeeze
   threshold lowered to <430px.
7. **Live-green-while-typing was reverted** per user request — green only
   appears after a step is locked in (Enter), not while a pending letter
   merely matches the goal position.
8. **Help/instructions card** rewritten for clarity: numbered steps (tap a
   letter → type a new letter to swap → hit return; real words lock in, a
   made-up word costs a step), full green/purple/red legend, example ladder
   COLD→CORD→WORD→WARD→WARM (top-down). Shows once via `localStorage` flag
   (`morf-helped2`), reopenable via the `?` button in the header.
9. **Tagline** under the mode tabs (`#hint`): "tap a tile · swap a letter ·
   lock it in".
10. Overlay/help cards use `margin: auto` + scrollable so they're never cut off
    on short viewports (fixed the "win screen truncated" bug).
11. The hidden keyboard input **blurs automatically** when any overlay
    (win/loss/help) opens, giving the card full screen.

## Visual design

- Dark theme: `--bg: #12131a`, `--bg2: #1b1d27`, `--text: #f2f3f7`,
  `--muted: #8b8fa3`.
- `--lock` (green, goal match): `#27b364`.
- `--changed` (purple, swapped letter): `#8e5bff`.
- Fumble red: `#c0334a`.
- `--accent` (brand pink): `#ff5d8f`.
- Confetti animation on wins; shake animation on fumbles/errors.
- PWA manifest (`manifest.json`) + OG/Twitter meta tags now added (2026-06-15).
  **Still need real image assets:** `icon-192.png`, `icon-512.png` (PWA icons)
  and `og.png` (social preview card) — currently referenced as placeholders.

## Git/PR history so far

1. `f872e95` — Initial BLURT! game (Wordle clone version, PR #1).
2. `237655e` — New-player onboarding, bigger dictionary, louder rejections.
3. `8d806df` — Replaced Wordle mechanic with word-ladder (major rewrite).
4. `3d9f954` — Fair par via common-words graph.
5. `73ba31d` — Native keyboard, live-green (later partially reverted), clearer
   help.
6. `858d143` — Upward climb direction + rung vocab (later reverted).
7. `20058a1` — Reverted to top-down + step vocab + keyboard-aware layout.
8. `01a4650` — Fixed smushed/squeeze layout bug.
9. `925cb39` — Fumble penalty + keyboard dismiss on cards.
10. `3575f42` — ENABLE dictionary swap + single-red-letter fumble styling.
11. `b7d58f2` — Tighten help copy, full color legend, punchier tagline.

All merged to `main`. GitHub Pages auto-deploys `main` via the standard "pages
build and deployment" workflow (typically green in 20-40 seconds).

**Verification approach used throughout:** headless Chromium via Playwright,
serving `index.html` over a local HTTP server, solving puzzles by computing
shortest paths independently in Node and driving the real UI (tile taps +
native keyboard input) to confirm scoring/par/share/persistence — no JS errors
in any verified pass.

## Go-to-market plan (discussed, not yet executed)

**Phase 0 — Foundation:** (mostly DONE as of 2026-06-15)
- ✅ Naming resolved (MORF) and full rename sweep executed; repo renamed.
- ✅ OG/Twitter meta tags added (social card *image* `og.png` still a
  placeholder — needs a real 1200×630 image).
- ✅ PWA `manifest.json` added (icon PNGs still placeholders).
- ✅ GoatCounter analytics wired (`morf.goatcounter.com`, site registered).
- ⏳ `morf.day` custom domain **not** pointed at Pages yet (user paused on it).
  `PLAY_URL` already targets `morf.day`, so until DNS/Pages config is done the
  share link won't resolve; working URL meanwhile is `beicholt.github.io/morf/`.

**Phase 1 — Viral loop sharpening:**
- **Challenge links** (seed-in-URL so recipients play the exact puzzle
  shared) — single best lever, not built.
- Past-days archive (future paywall candidate).
- Launch sequence: group chats first → r/wordgames, r/WebGames,
  r/InternetIsBeautiful → Show HN → Product Hunt → optional TikTok.

**Phase 2 — Retention:**
- Streak freeze (1 free skip/week).
- Stats page, 5-letter hard mode.

**Phase 3 — Monetization (sequenced, don't skip ahead):**
1. Tip jar (Ko-fi/Buy Me a Coffee) — now.
2. Single native sponsor line at ~10k DAU.
3. Freemium (NYT Games model): free daily forever, $25-30/yr premium for
   archive/unlimited endless/hard mode/stats/streak insurance — requires a
   small backend (Cloudflare Workers + Stripe).
4. Long-shot exit: acquisition by a publisher (Wordle precedent).

**Expectations / decision rule:**
- ~75% chance: stays a small friends-and-Reddit project, $0 income.
- ~20% chance: niche following, $20-300/mo from tip jar + small sponsor.
- Low-single-digit %: viral spike, $500-3k/mo while it lasts.
- <<1%: acquisition.
- Run a 90-day experiment after launch: if share rate >~10% of completed
  dailies AND D7 retention >~15%, double down; otherwise leave it live as a
  free portfolio piece and move on — no sunk-cost spiraling.

## Immediate next steps / open items

1. ✅ ~~Decide the final name~~ — **MORF**, rename sweep executed 2026-06-15.
2. ✅ ~~PLAY_URL / Pages URL discrepancy~~ — repo renamed `morf`; `PLAY_URL` →
   `morf.day`; README link → `beicholt.github.io/morf/`.
3. ✅ ~~Phase 0 GTM: OG tags, PWA manifest, analytics~~ — all added. Remaining:
   create real image assets (`icon-192.png`, `icon-512.png`, `og.png`).
4. ⏳ **Wire up `morf.day`** (custom domain → Pages + DNS / `CNAME`) when ready
   — currently paused. Until then the in-game share link won't resolve.
5. Challenge-link feature (Phase 1) still not started.
6. `README.md` updated for the rename. Re-check it once `morf.day` is live (the
   "Play now" link currently uses the Pages URL).
