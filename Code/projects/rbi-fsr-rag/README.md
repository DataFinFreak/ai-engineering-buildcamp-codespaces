# RAG over RBI Financial Stability Reports - and why it confidently gave the wrong year

A retrieval-augmented generation (RAG) pipeline over four of the Reserve Bank of India's
Financial Stability Reports (Dec 2024 - Jun 2026, 732 pages). It worked, and it showed a failure that
is common in RAG over periodic reports: **asked for a bank ratio, it answered with last year's
figure, and gave 0.95 confidence on wrong answers.**

Built as the documents mini-project for the AI Bootcamp: From RAG to Agents (Alexey Grigorev). Everything is in one notebook: [`rbi_fsr_rag.ipynb`](rbi_fsr_rag.ipynb).

## The finding

The question was *"gross NPA ratio of scheduled commercial banks"*. The latest report (June 2026)
says it fell to **1.8% in March 2026**. The RAG answered **2.3%, as of March 2025**.

| version | answer | input tokens |
|---|---|---|
| baseline RAG | 2.3% (March 2025) | 9,936 |
| + "prefer the latest report" instructions | 2.3% (March 2025), now naming the June 2025 report | 9,991 |
| + top 2 chunks from each report | 2.6% (September 2024) | 14,850 |

Prompt fixes didn't help, because **the chunk with the 1.8% sentence was never retrieved.** It wasn't
in the top 5 for any wording, even with the search restricted to the June 2026 report. Three things
stack up:

1. **Keyword search has no notion of time.** BM25/TF-IDF-style ranking favours the chunk that says
   "GNPA" 11 times (June 2025), whatever its date.
2. **The vocabulary doesn't match.** People ask about "gross NPA" and "scheduled commercial banks", but
   the reports write "GNPA" and "SCBs".
3. **The PDF extraction mixes up columns.** Two-column pages come out interleaved
   (`...declining to a multi- 2.8 The provisioning coverage ratio...`), which dilutes the match.

## Tough-question test: 2 of 5 right

Five hard questions, with the correct answers looked up in the reports by hand:

| question | RAG | correct | confidence | verdict |
|---|---|---|---|---|
| GNPA, March 2025 -> March 2026 | 2.5% -> 2.3% | 2.3% -> 1.8% | 0.95 | wrong |
| Stress test (Shock 2): GNPA assumed, banks breaching CRAR | 8.1%, four banks | 8.1%, four banks | 0.90 | right |
| Provisioning coverage ratio, March 2025 vs March 2026 | 76.3% -> 72.6% | 76.3% -> 75.6% | 0.95 | wrong |
| Main risks in the June 2026 report | 6 plausible risks | - | 0.90 | ungrounded: no June 2026 chunk was retrieved |
| RBI repo rate decision, October 2026 (not in any report) | "not in the context" | not in any report | 0.30 | right |

**Takeaways**
- **The self-reported confidence score doesn't measure correctness.** It was 0.95 on wrong answers.
- **Telling the model to "prefer the latest report" backfired:** it labelled answers "June 2026" even
  when no chunk from that report had been retrieved. The citation should come from the chunk's metadata,
  not from the model.
- **It refused the out-of-scope question correctly**, and gave exact numbers when the right chunk *was* retrieved.
- **The bottleneck is retrieval, not the LLM.**

## Pipeline

```
rbi_fsr.csv -> download PDFs -> markitdown (PDF -> Markdown) -> drop blank lines
            -> sliding-window chunks (100 lines, step 50) -> minsearch keyword index
            -> top-5 search -> gpt-4o-mini -> answer (plain text, then Pydantic structured output)
```

| step | result |
|---|---|
| Reports converted | 4 PDFs -> 2.7M characters of Markdown |
| Chunks (size=100, step=50) | 801 |
| One RAG query | 9,750 input tokens, 89 output |
| Structured output (6-field Pydantic schema) | +186 input tokens |

Stack: Python 3.14, `uv`, `requests`, `markitdown[pdf]`, `gitsource` (chunking), `minsearch`
(keyword search), OpenAI Responses API (`gpt-4o-mini`), Pydantic.

## What I'd do next

- **Retrieval:** smaller chunks, hybrid search (keyword + vector embeddings) to bridge "gross NPA" and
  "GNPA", and a recency boost or filter on report date.
- **Extraction:** column-aware, page-aware PDF parsing, which also enables page-level citations.
- **Grounding:** cite the report from chunk metadata, not from the model's own claim.
- **Evaluation:** use the 5 tough questions as a regression set and re-run them after each change.

## Run it

```bash
git clone https://github.com/DataFinFreak/ai-engineering-buildcamp-codespaces
cd ai-engineering-buildcamp-codespaces
uv sync
echo "OPENAI_API_KEY=sk-..." > .env
uv run jupyter lab   # open Code/projects/rbi-fsr-rag/rbi_fsr_rag.ipynb and run all
```

The notebook downloads the PDFs itself; they aren't stored in the repo. The RBI site has bot
protection, so the download sends a browser User-Agent and checks that each file starts with `%PDF-`.
A full run costs about a cent of `gpt-4o-mini`.

## Source and credit

Data: [Financial Stability Report](https://www.rbi.org.in/Scripts/FsReports.aspx), Reserve Bank of India,
editions December 2024, June 2025, December 2025 and June 2026. © Reserve Bank of India. Reproduction is
permitted provided an acknowledgment of the source is made. This project is not affiliated with the RBI.

---

*Tags: RAG, retrieval-augmented generation, LLM, RBI, Financial Stability Report, NPA, Indian banking,
minsearch, markitdown, OpenAI, structured output, Pydantic, LLM evaluation.*
