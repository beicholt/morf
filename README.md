# MORF 🪜

A stupidly simple, highly addictive word-morphing game.

**▶ Play now: https://beicholt.github.io/morf/**

## How to play

Turn the top word into the **goal word**, swapping **one letter per step**.
Every step must be a real word:

```
COLD → CORD → WORD → WARD → WARM
```

Tap a tile, type a letter, hit **ENTER**. Letters that already match the goal
glow green. Fewer steps = bigger flex — every puzzle shows its **perfect path**
(the true shortest route through common words), and matching it is the whole obsession.

The mechanic is Lewis Carroll's *Doublets* (1879) — a 147-year-old classic,
rebuilt for the group-chat era.

## Two modes

- **DAILY** — everyone on the planet gets the *same* puzzle each day, one
  attempt only. Solve it to grow your day streak.
- **ENDLESS** — a new puzzle the second you finish one. The streak only grows
  on **perfect paths**, so it stays spicy.

## Share & flex 📣

Every finished puzzle has a **SHARE** button that produces a spoiler-free step
trail with your score — ready for the group chat:

```
MORF Daily #163 — VICE ➜ WERE
3 steps · perfect is 3 🔮
🟩⬜⬜🟩
🟩⬜🟩🟩
🟩🟩🟩🟩
Find a shorter path.
```

Ranks: 🦅 BEYOND PERFECT → 🔮 PERFECT PATH → 🧠 SO CLOSE → 🔥 SOLID → 😅 GOT THERE.

Streaks, bests, and solves are saved in your browser per mode.

## Run it locally

No build, no dependencies — it's one HTML file. Open `index.html` in any
browser, or serve it:

```sh
python3 -m http.server 8000
```
