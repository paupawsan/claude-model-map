# Claude Model Route Map

A single-file, bilingual (EN/JA) decision chart for picking a Claude model — and the effort level to run it at — across general use and agentic development.

Accurate as of **July 2026**. Covers Claude Fable 5, Opus 5, Sonnet 5, and Haiku 4.5.

**Live:** https://paupawsan.github.io/claude-model-map/ — with a one-page [quick reference](https://paupawsan.github.io/claude-model-map/claude-model-quickref.html) alongside it.

---

## Quick start

1. Open `index.html` in any browser. No build step, no dependencies — and opened locally, no network calls (see [Hosting and analytics](#hosting-and-analytics)).
2. Tap **EN / 日本語** in the header to switch languages. Every string exists in both.
3. `Ctrl/Cmd + P` to print — the stylesheet has a print mode that flattens the dark panels for paper.

The whole thing is one file. Drop it on a static host, attach it to a wiki page, or keep it local.

---

## How to read it

The chart is organized by **scale**, not by model. Which question you're answering depends on how much of the system you're deciding for.

### 01 — Per call

Two lanes:

- **Lane A (general use)** — chat, writing, research, code. Branches on whether the task needs real judgment, and whether being wrong is expensive.
- **Lane B (agentic development)** — branches on whether the call *decides what happens next* or *executes a defined step*. That split, not task difficulty, is what picks the tier in an agent loop.

Each destination carries a model **and** a suggested effort level.

### 02 — Per thread

What to hold constant once a single call becomes a conversation. The load-bearing item: effort is a per-request setting, but swapping depth mid-thread makes behaviour inconsistent from turn to turn. Pick a level at the start of a workload and keep it. Also covers prompt-cache minimums, which differ by model.

### 03 — Per loop

Multi-call, multi-agent territory. Effort tunes *depth per step*; task budgets tune *breadth across the loop*. Also covers subagent isolation, session-level caps, and the fallback ladder.

### 04–06 — Reference

The five effort levels, a use-case table with recommended effort per row, and per-model detail cards with pricing, context window, max output, and knowledge cutoff.

---

## Customizing

Everything lives in the `:root` block and the markup. No framework.

| What | Where |
| --- | --- |
| Model line colors | `:root` → `--haiku`, `--sonnet`, `--opus`, `--fable` |
| Palette / typography | `:root` → `--paper`, `--ink`, `--sans`, `--mono` |
| Add or edit a bilingual string | Add a `<span data-en>` and a `<span data-ja>` side by side |
| Change a route | Edit the nested `.junction` / `.exits` / `.exit` blocks |
| Add a model | Copy a `<section class="board">` and set `style="--c:var(--yourcolor)"` |

**Bilingual rule:** every user-facing string is a pair of sibling spans, one tagged `data-en` and one `data-ja`. The toggle hides one set with a `data-lang-hide` attribute. If you add a string in one language only, it will show in both — that's the failure mode to watch for.

The language toggle is ~15 lines of vanilla JS at the bottom. Default language is set by the final `setLang('en')` call.

---

## Hosting and analytics

The site is published with GitHub Pages: **Settings → Pages → Source: Deploy from a branch → `main` / `/ (root)`**. There is no build step, so nothing else is needed — `index.html` is served at the Pages root and `claude-model-quickref.html` at its own filename. The empty `.nojekyll` file disables Jekyll processing, which would otherwise silently drop any path beginning with an underscore.

Pages gives you no server logs, so traffic is measured client-side by [GoatCounter](https://www.goatcounter.com/) — a ~3.5 KB script at the bottom of both HTML files. It sets no cookies, stores no personal data, and creates no cross-site identifier, so no consent banner is required under GDPR or the APPI.

Two properties worth knowing:

- **It only runs on the published site.** The block is gated on `location.hostname === 'paupawsan.github.io'`, so opening the file locally, serving it from a wiki, or forking it to another host makes no network calls whatsoever. Change or drop that hostname check if you fork and want stats of your own.
- **Removing it is deleting one block.** The `<!-- Analytics: GoatCounter -->` script near `</body>` in each file is the whole integration. Nothing else references it.

Swapping providers (Cloudflare Web Analytics, Plausible, Fathom) means replacing that one block with their tag. Google Analytics is the exception worth avoiding here: it sets cookies, so it would need a consent banner, and every string in this project has to exist in both English and Japanese.

---

## Keeping it current

Model names, pricing, and effort support change often. Check against the source before relying on a number:

- Models and pricing — https://platform.claude.com/docs/en/about-claude/models/overview
- Effort parameter — https://platform.claude.com/docs/en/build-with-claude/effort
- Task budgets (beta) — https://platform.claude.com/docs/en/build-with-claude/task-budgets

Figures baked into the chart as of this version: Fable 5 $10/$50 per MTok, Opus 5 $5/$25, Sonnet 5 $3/$15 (introductory $2/$10 through 2026-08-31), Haiku 4.5 $1/$5.

Two things that are easy to get wrong and worth re-checking after any model release:

- **Haiku 4.5 does not support the effort parameter.** There is no depth dial on that tier.
- **Task budgets are beta** (`task-budgets-2026-03-13` header), supported on Opus 5, Fable 5, and Sonnet 5 — not Haiku 4.5.
- **Fable 5 fallback is opt-in on the API.** A declined request returns HTTP 200 with `stop_reason: "refusal"`; nothing retries on another model unless you configure it.

---

## 日本語

`index.html` をブラウザで開くだけで使えます。ヘッダの **EN / 日本語** で言語を切り替え、`Ctrl/Cmd + P` で印刷できます。ビルド不要・依存なしの単一ファイルです。外部通信は、公開サイト（`paupawsan.github.io`）でのアクセス解析ビーコン 1 件のみ。ローカルで開いた場合や別ホストに置いた場合は一切通信しません。

構成は「スケール」順です。**01 呼び出し単位**でモデルと effort を選び、**02 スレッド単位**で固定すべきものを決め、**03 ループ全体**で予算と受け渡しを設計します。04〜06 は effort の 5 段階、使い分け表、各モデルの詳細（価格・コンテキスト・最大出力・知識カットオフ）です。

文字列はすべて `data-en` と `data-ja` の兄弟 `<span>` の組になっています。片方だけ追加すると両言語で表示されてしまうので注意してください。色とフォントは `:root` にまとまっています。

価格や対応状況は変わります。上記の公式ドキュメントで確認してから使ってください。

---

## Files

| File | Purpose |
| --- | --- |
| `index.html` | The chart. Self-contained. Served at the Pages root. |
| `claude-model-quickref.html` | One-page quick reference — the same guidance without the decision tree. |
| `LICENSE` | MIT license text. |
| `README.md` | This file. |
| `.nojekyll` | Empty marker that tells GitHub Pages to serve files as-is, without Jekyll. |

Authorship and license are also embedded in the HTML `<head>` as standard meta tags, Dublin Core terms, `<link rel="license">`, and schema.org JSON-LD.

---

## Credits

Co-authored with Claude (Anthropic). Research, drafting, and the bilingual copy were produced in collaboration with the model; every factual claim was verified against Anthropic's published documentation before release.

## License

MIT — see `LICENSE`. Free to use, modify, and redistribute, including commercially, as long as the copyright notice and license text are retained.

Copyright (c) 2026 Paulus Ery Wasito Adhi &lt;paupawsan@gmail.com&gt;
