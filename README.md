# RAG Assignment — Baseline vs. Re-Ranking

Two-stage retrieval over a single complex PDF, comparing a dense **Bi-Encoder** baseline
against a **Cross-Encoder** re-ranker, then feeding the selected chunks to an LLM.

| | |
|---|---|
| **Dataset** | CATAN base-game *Game Rules & Almanac* (official rulebook, `catan_rulebook.pdf`, 16 pages) |
| **Bi-Encoder (retriever)** | `sentence-transformers/all-MiniLM-L6-v2` |
| **Cross-Encoder (re-ranker)** | `cross-encoder/ms-marco-MiniLM-L-6-v2` |
| **LLM (generation)** | Gemini API (`gemini-flash-lite-latest`) |

## Files
- `code.ipynb` — the full assignment (run top to bottom).
- `report.tex` / `report.pdf` — written report in LaTeX (architecture, rank-shift, latency, scaling). *Rewrite in your own words before submitting — see note inside.* Rebuild with `tectonic report.tex` (or `pdflatex report.tex`).
- `catan_rulebook.pdf` — the source document.
- `requirements.txt` — dependencies.

## Pipelines
- **Pipeline 1 (Baseline):** Bi-Encoder embeds + indexes chunks → retrieve **Top 10** by cosine similarity → take **Top 3** → LLM → **Answer 1**.
- **Pipeline 2 (Re-Ranking):** take the same **Top 10** → score all 10 `(query, chunk)` pairs with the Cross-Encoder → re-sort → **Top 3** → LLM (identical prompt) → **Answer 2**.

## How to run
```bash
conda activate global          # or your env
pip install -r requirements.txt
jupyter notebook code.ipynb
```
1. **Add your Gemini key** to a `.env` file in this folder (it is git-ignored):
   ```
   GEMINI_API_KEY=your_key_here
   ```
   Get a key at https://aistudio.google.com/apikey.
2. Open the notebook and run cells top to bottom. The first run downloads the two models (~160 MB).
   The notebook loads the key from `.env` automatically. Everything except the final LLM answer
   (retrieval, re-ranking, rank-shift, latency) runs even without a key.

> **Security:** never commit `.env` or paste the key into the notebook — the repo is public.

## Source of the dataset
Official CATAN rulebook PDF, downloaded from catan.com.
