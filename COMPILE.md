# ICLR 2026 LaTeX 投稿版 — 编译说明

## 文件清单

- `main.tex`：论文正文（由 `tools/md2tex.py` 从 `paper-draft.md` 自动转换生成）
- `iclr2026_conference.sty` / `iclr2026_conference.bst`：ICLR 2026 官方模板
  （<https://github.com/ICLR/Master-Template/raw/master/iclr2026.zip>）
- `natbib.sty` / `fancyhdr.sty` / `math_commands.tex`：官方包内附带文件
- `figures/`：5 张论文图（fig1–fig5）
- `main.pdf`：已编译好的 PDF（XeLaTeX 编译，14 页）

## 本地编译

需要 XeLaTeX（中文支持）：

```bash
xelatex -interaction=nonstopmode main.tex
xelatex -interaction=nonstopmode main.tex
```

依赖：`texlive-xetex`、`texlive-lang-chinese`（含 Noto CJK 字体）、
`texlive-latex-recommended`、`texlive-latex-extra`。
参考文献为手写 `thebibliography`，无需 bibtex。

## 当前状态

- 匿名投稿模式：`\iclrfinalcopy` 保持注释，显示 "Anonymous authors" 和
  "Under review as a conference paper at ICLR 2026"。
- Camera-ready 时：取消注释 `\iclrfinalcopy` 并填写 `\author{}`。

## ⚠️ 投稿前必须处理

1. **页数超限**：ICLR 2026 要求正文 ≤ 9 页（严格执行，超页直接 desk-reject；
   参考文献不限页数）。当前正文约 12 页、参考文献约 2 页，
   需压缩约 3 页正文（建议：合并表格、精简 §2 相关工作、把部分细节移入附录）。
2. 正文仍有「本文首次」「首个中文……」等表述，novelty 未经严格核验，建议弱化。
3. 数据集尚未公开，「公开的中文冲突评测集」表述需改为「将公开」或先完成公开。
4. 缺：作者信息、英文版、AI 使用声明（ICLR 要求披露 LLM 在写作中的作用）。
