# Credibility Adjudication for Memory Conflicts in Chinese Agent Memory

**A Bayesian Framework and an Empirical Study of Chinese Pragmatic Signals**

> ICLR 2026 submission — anonymous review version.
> LaTeX source, figures, and the compiled PDF are all included.

---

## TL;DR

Long-running AI agents accumulate contradictory claims about the same fact. Most
systems resolve them by **recency** (last-write-wins) or by an ad-hoc **LLM judgement**
at read time. Credibility-conditioned Bayesian adjudication has been explored in
English, but whether it transfers to Chinese — where epistemic modality, register,
and coreference behave differently — had never been tested.

This is, to our knowledge, the **first empirical study of credibility-based memory-conflict
adjudication in Chinese**. We build a 240-item Chinese conflict benchmark
(`conflict-cn v1.0`, aligned with the LongMemEval taxonomy), 45% of which are
**adversarial items where the *older* claim is the credible one** — these separate
credibility adjudication from the recency heuristic.

**Headline:** the Bayesian paradigm does transfer (0.9833 for an English-method
replication), adding Chinese-specific pragmatic signals pushes it to 1.0000, and
spending ~48k real LLM tokens on detection buys **zero** accuracy gain.

---

## Key results (n = 240, `conflict-cn v1.0`)

| Method | Overall acc. [95% CI] | Adversarial (n=108) | Non-adv. (n=132) | Miscoverage | LLM calls |
|---|---|---|---|---|---|
| B1 last-write-wins | 0.5500 [0.4868, 0.6117] | 0.0000 | 1.0000 | 1.0000 | 0 |
| B2 rule-detect + recency | 0.5500 [0.4868, 0.6117] | 0.0000 | 1.0000 | 1.0000 | 0 |
| B3 LLM zero-shot (DeepSeek, real calls) | 0.9625 [0.9303, 0.9801] | 0.9167 | 1.0000 | 0.0833 | 240 |
| B4 Bayesian replication (Nous, English-specific parts removed) | 0.9833 [0.9579, 0.9935] | 1.0000 | 0.9697 | 0.0000 | 0 |
| **Ours (full model)** | **1.0000 [0.9842, 1.0000]** | 1.0000 | 1.0000 | 0.0000 | 0 |
| Ours + LLM detection (DeepSeek, real calls) | 1.0000 [0.9842, 1.0000] | 1.0000 | 1.0000 | 0.0000 | 240 |

Reading of the table:

- **B1/B2 score 0/108 on the adversarial subset.** The recency heuristic fails
  systematically when the older claim is the trustworthy one — this is the paper's motivation.
- **B4 reaches 0.9833 on Chinese data**, so the Bayesian paradigm largely holds across
  languages (answer to RQ1). Its 4 errors all fall in the non-adversarial subset.
- **Ours is 240/240, but the 4-item gap over B4 is not significant** (McNemar exact,
  p = 0.125). We deliberately do not oversell "1.0000 vs 0.9833".
- **B3 (pure LLM zero-shot) is 0.9625, and all 9 errors are adversarial.** Its failure mode
  is a *confidence–recency bias*: a confidently-worded new claim overrides the hedge signal.
  Its errors do not overlap with B4's — the two families trip on different traps.
- **Swapping rule-based detection for LLM detection changes nothing** (per-item identical).
  The LLM detector is in fact more conservative (127/240 conflicts detected vs 240/240),
  but because detection and adjudication are decoupled, accuracy is unaffected.

### Ablation: what the Chinese signals are worth

Chinese pragmatic signals contribute **+30.00 pp** overall (McNemar, p = 4.2 × 10⁻²²):

- **Hedge strength (three-level)** dominates: **+13.33 pp**
- **Register** shows a redundancy–complementarity pattern
- **Corroboration count** is not identifiable on the current data

---

## Method in one paragraph

We formalise three Chinese pragmatic signals — **three-level hedge strength**, **register**,
and **coreference risk** — as reliability features in a **triplet credibility model**
(source-type prior, pragmatic features, corroboration count), combined with **Bayesian
posterior adjudication**, a **source cap**, and a **shadow-evidence audit trail**.
Adjudication is decoupled from detection, so a missed detection degrades gracefully
rather than catastrophically.

---

## Repository layout

```
.
├── main.tex                    # Paper source (generated from paper-draft.md by tools/md2tex.py)
├── math_commands.tex           # Math macros shipped with the ICLR template
├── main.pdf                    # Compiled PDF (XeLaTeX, 14 pages)
├── figures/
│   ├── fig1-framework.png      # Method framework
│   ├── fig2-main-results.png   # Deterministic-method accuracy (95% Wilson CI)
│   ├── fig3-ablation.png       # Ablation contribution of Chinese signals
│   ├── fig4-cost-accuracy.png  # RQ3 cost–accuracy trade-off
│   └── fig5-robustness-topic.png # Leave-one-topic-out robustness
├── iclr2026_conference.sty     # ICLR 2026 official template
├── iclr2026_conference.bst     #   (https://github.com/ICLR/Master-Template)
├── natbib.sty / fancyhdr.sty   # Bundled dependencies
├── COMPILE.md                  # Build instructions (Chinese)
└── README_CN.md                # 中文版说明
```

## Compiling

Requires XeLaTeX (CJK support):

```bash
xelatex -interaction=nonstopmode main.tex
xelatex -interaction=nonstopmode main.tex
```

Dependencies: `texlive-xetex`, `texlive-lang-chinese` (Noto CJK fonts),
`texlive-latex-recommended`, `texlive-latex-extra`.
References are a hand-written `thebibliography` — no BibTeX run needed.

---

## Status and known gaps

This is an **anonymous submission build**: `\iclrfinalcopy` stays commented out, so the
PDF renders "Anonymous authors" and "Under review as a conference paper at ICLR 2026".
At camera-ready, uncomment `\iclrfinalcopy` and fill in `\author{}`.

Open items we are aware of (see `COMPILE.md` for the full list):

1. **Page limit.** ICLR 2026 enforces ≤ 9 pages of main content (references unlimited);
   the current build is ~12 + 2 pages. Needs ~3 pages cut (merge tables, compress §2,
   move details to the appendix).
2. **Novelty claims** ("first", "first Chinese …") still need toning down / verification.
3. **Dataset release.** `conflict-cn v1.0` is not yet public; wording must say "to be
   released" until it is.
4. **Missing:** author information, an English-language version, and the mandatory
   ICLR LLM-usage disclosure.

## License

MIT — see [LICENSE](LICENSE). Copyright is held by "Anonymous Authors" during the
double-blind review period and should be updated at camera-ready.
