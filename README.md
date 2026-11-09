# IMF Article IV Report → Text-to-Dashboard LLM Pipeline

Code for **7PAM2032 Data Analysis with AI — Assignment 1**. The notebook turns the 2023 IMF Article IV
Staff Report for Israel (IMF Country Report No. 23/211) into summaries, structured indicators, a grounded RAG Q&A system and an
executive dashboard, with an LLM-as-a-Judge evaluation at each stage.

## Pipeline

```
PDF ─► pypdf text ─► boilerplate removal & line repair ─► TOC-based section detection
    ─► sentence-aware chunks (300–500 tokens, 50 overlap)
         ├─► Task 1  executive summary + section summaries (<100 words) ─► judge (consistency / completeness / factual alignment)
         ├─► Task 2  theme counts (lexicon + LLM) · strengths & vulnerabilities (quote-verified)
         │           · core-indicator JSON with fallbacks ─► judge audit · table time series (value-verified)
         ├─► Task 3  bge-small embeddings ─► FAISS IndexFlatL2 ─► grounded answers vs closed-book ─► RAG-triad judge
         │           · extension: multi-query RAG with reciprocal rank fusion
         └─► Task 4  pd.melt ─► 4-panel dashboard (300 dpi)
```

## Running it

1. Open `IMF_Article_IV_LLM_Pipeline.ipynb` in Google Colab.
2. Add a Colab secret `GEMINI_API_KEY` and switch on notebook access.
3. Provide the report: set `CONFIG["PDF_URL"]`, or upload the PDF when prompted.
4. Optional: set `CONFIG["REPO_URL"]` to this repository's URL to restore the committed `cache/`,
   which reproduces the submitted results exactly, with no API calls.
5. Runtime → Run all.

## Outputs

| Folder | Contents |
|---|---|
| `outputs/` | `task1_*.json` summaries and judge scores · `task2_*.json` themes, qualitative insights, indicators (target schema), evidence, audit, trends · `task3_*.json` RAG answers and evaluation · `llm_call_log.csv` |
| `figures/` | `executive_dashboard.png` and supporting figures, all 300 dpi |
| `cache/` | Hashed LLM responses and embeddings, used for reproducible re-runs |

## Models and libraries

* Google Gemini (generator `gemini-3.5-flash-lite`, judge `gemini-3.5-flash`) via `google-genai`
* `BAAI/bge-small-en-v1.5` sentence embeddings (Xiao et al., 2023) via `sentence-transformers`
* FAISS (Johnson, Douze & Jégou, 2019), pypdf, pandas, seaborn, Pydantic

## Data

The source document is an IMF Country Report, © International Monetary Fund. It is not
redistributed in this repository; download it from the IMF eLibrary.

## Licence

MIT. See `LICENSE`.
