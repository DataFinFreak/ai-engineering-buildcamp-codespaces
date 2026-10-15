# Learning resources

Companion sheet for this repo. Sections follow the order the concepts actually show up in the
notebooks, not the order a textbook would teach them. Each section has **Videos**, **Reading**,
and **Books**.

**How to use it:** don't read top to bottom. Find the section matching whatever you're stuck on,
watch the one item marked ⭐, and stop there. Everything below the ⭐ in each section is optional
depth for later.

**Legend**

| Mark | Meaning |
|---|---|
| ⭐ | Start here — the single best entry point for that concept |
| 🆓 | Free and legal to read online in full |
| 💰 | Paid book |
| ❓ | Link exists but not personally watched/read — judge it yourself |

> All links were checked to resolve at the time of writing. Books are confirmed against
> publisher listings (title, author, publisher, year). Videos are marked ❓ because existing
> is not the same as being good.

---

## 00 — Study tactics

Worth two minutes before anything else.

- **Course videos with a difficult accent:** turn on YouTube captions and set playback to
  0.75x–0.9x. Faster than hunting for substitute material, and the course content is specific
  to these homework assignments in a way generic videos never will be.
- **Don't chase the maths.** TF-IDF, cosine similarity and B-trees are mechanisms you need to
  *recognise*, not derive. One clear explanation each is enough; move on.
- **Build before you read.** This repo's notebooks are the point. Videos are for unblocking,
  not for front-loading theory.

---

## 01 — LLMs and the OpenAI API

*Where in the repo:* `01-foundation/02-rag/openai.ipynb`

**Videos**
- ⭐ ❓ [Chat with Your Own Data: Introduction to the LLM Zoomcamp](https://www.youtube.com/watch?v=q-p36Ak6YI8) — Alexey Grigorev. Sets up the whole mental model this course builds on.
- ❓ [LLM Zoomcamp 2026 Course Launch](https://www.youtube.com/watch?v=7TuZTVwnmhk) — Alexey Grigorev.

**Reading**
- 🆓 [OpenAI API documentation](https://platform.openai.com/docs/overview) — the text generation and embeddings guides are the two that matter here.
- 🆓 [Claude API documentation](https://docs.claude.com) — for comparison; same concepts, different SDK.

**Books**
- 💰 **AI Engineering: Building Applications with Foundation Models** — Chip Huyen, O'Reilly, 2025. The best single book for this entire course. Chapter 6 is "RAG and Agents", which is essentially the course syllabus. If you buy one book, buy this one.
- 💰 **Hands-On Large Language Models** — Jay Alammar & Maarten Grootendorst, O'Reilly, Sept 2024. More visual and gentler than Huyen; strong chapters on semantic search and dense retrieval. Alammar wrote the widely-used "Illustrated Transformer" posts, so the diagrams are excellent.

---

## 02 — Getting documents (ingestion)

*Where in the repo:* `01-foundation/02-rag/rag.ipynb`, cells 1–40

Downloading a repo zip, reading `.md`/`.mdx` out of memory, splitting YAML frontmatter from body
text, and ending up with a list of dicts.

**Reading**
- 🆓 [`zipfile`](https://docs.python.org/3/library/zipfile.html) and [`io.BytesIO`](https://docs.python.org/3/library/io.html#io.BytesIO) — Python stdlib. The trick in the notebook is reading a zip straight from bytes, so nothing touches disk.
- 🆓 [`python-frontmatter` docs](https://python-frontmatter.readthedocs.io/) — `frontmatter.loads()` splitting metadata from content.
- 🆓 [`requests` quickstart](https://requests.readthedocs.io/en/latest/user/quickstart/)
- ❓ [`gitsource` on PyPI](https://pypi.org/project/gitsource/) — the library that wraps all of the above into three lines.
- 🆓 [Evidently docs](https://docs.evidentlyai.com) — the actual corpus being indexed, worth skimming so you know what the search results mean.

**Books**
- No book needed. This is plumbing — the library docs beat any book chapter here.

---

## 03 — Keyword search and indexing ⬅ *you are here*

*Where in the repo:* `01-foundation/02-rag/rag.ipynb`, cells 41–49 (the Search section)

The concept: `minsearch.Index` with `text_fields` and `keyword_fields`, `fit()` building TF-IDF
matrices, `search()` scoring by cosine similarity.

**First, a distinction that saves you an hour.** "Indexing" means two unrelated things:

| | Database indexing | Search indexing |
|---|---|---|
| **Question it answers** | `WHERE user_id = 42` | "find pages about LLM as a judge" |
| **Structure** | B-tree / B+tree | Inverted index, TF-IDF matrix |
| **Result** | Exact match, or nothing | Everything, ranked by relevance |
| **Used by** | Postgres, MySQL | Elasticsearch, **minsearch** |

Both precompute a lookup structure so you don't rescan everything — that shared idea is why they
share the name. **This notebook is the right-hand column.** Videos about B-trees are useful
general knowledge but will not explain this code.

**Videos — search indexing (the relevant one)**
- ⭐ ❓ [Implement a Search Engine](https://www.youtube.com/watch?v=nMrGK5QgPVE) — Alexey Grigorev. This workshop is where `minsearch` came from; he builds it from scratch, text search then vector search. Nothing else maps this directly onto the notebook.
- ❓ [Database Indexing Explained: B-Tree vs Full-Text Index](https://www.youtube.com/watch?v=_pKudLsENYg) — covers both senses of the word and contrasts them. Good if the table above didn't land.

**Videos — database indexing (adjacent, skippable for this course)**
- ❓ [Database Indexing Explained](https://www.youtube.com/watch?v=k1eyF7lEO1U)
- ❓ [Understanding B-Trees: The Data Structure Behind Modern Databases](https://www.youtube.com/watch?v=K1a2Bk8NrYQ)
- ❓ [B-Trees, Indexing and TeraBytes of Data](https://www.youtube.com/watch?v=CpCUZRQGkcs)
- ❓ [SQL Indexes Explained — B-Tree Indexes in MySQL](https://www.youtube.com/watch?v=Clr2cFrIJpA)

**Reading**
- 🆓 [`minsearch` source on GitHub](https://github.com/alexeygrigorev/minsearch) — ~200 readable lines. `fit()` and `search()` are the whole library. Also sitting locally in `.venv/Lib/site-packages/minsearch/`.
- 🆓 [scikit-learn: text feature extraction](https://scikit-learn.org/stable/modules/feature_extraction.html#text-feature-extraction) — the TF-IDF section gives the actual weighting formula. minsearch is a thin wrapper over this.
- 🆓 [`TfidfVectorizer` API reference](https://scikit-learn.org/stable/modules/generated/sklearn.feature_extraction.text.TfidfVectorizer.html)

**Books**
- ⭐ 🆓 **Introduction to Information Retrieval** — Manning, Raghavan & Schütze, Cambridge University Press, 2008. [Full text free at Stanford](https://nlp.stanford.edu/IR-book/). *The* canonical text on this topic. Read **Chapter 1** (inverted index — what a search index actually is) and **Chapter 6** (term weighting, TF-IDF, the vector space model). Pre-dates LLMs entirely, which is fine: this layer hasn't changed.
- 💰 **Relevant Search** — Doug Turnbull & John Berryman, Manning Publications, 2016. All about *tuning* relevance — field boosting, why your top result is wrong, how to fix it. Directly applicable the moment you reach for `boost_dict`.
- 💰 **Designing Data-Intensive Applications** — Martin Kleppmann, O'Reilly. Chapter 3, "Storage and Retrieval", is the best explanation of B-trees and LSM-trees in print. This is the *database* column of the table above — career-valuable, not course-required.

---

## 04 — Chunking

*Where in the repo:* **not implemented yet.** The notebook indexes whole pages.

Normally chunking comes *before* indexing: you split documents into pieces, then index the pieces,
because the chunk is what you want retrieved. Indexing whole pages costs you twice — the answer
gets diluted across a long document during scoring, and you end up pasting entire pages into the
prompt later.

**Reading**
- 🆓 `gitsource.chunking` — `sliding_window()` and `chunk_documents()` are already installed and ready to use. Read the source at `.venv/Lib/site-packages/gitsource/chunking.py`.
- Huyen's *AI Engineering* ch. 6 (retrieval optimization) for how chunk size trades off against retrieval quality.

**Books**
- 💰 *AI Engineering* (above) — retrieval optimization section.
- 💰 *Hands-On Large Language Models* (above) — semantic search chapter.

---

## 05 — Embeddings and vector search

*Where in the repo:* **not reached yet.** This is the fix for everything keyword search gets wrong.

The problem it solves: TF-IDF matches literal words, so it misses synonyms ("model-based
evaluation" vs "LLM as a judge") and cannot tell word senses apart (an LLM judge vs a legal
judge). Embeddings put *meaning* in the vector instead of word counts.

**Videos**
- ⭐ ❓ [What is a Vector Database?](https://www.youtube.com/watch?v=EpmIIDC2Xgg) — Pinecone. Mild vendor marketing, sound explanation.
- ❓ [Embeddings & Vector Databases Explained](https://www.youtube.com/watch?v=rw1YfQQttfo) — alternative if the above doesn't click.
- ❓ [AI Embeddings Explained: Vectors Behind RAG Search](https://www.youtube.com/watch?v=NjMK2Hignh4) — visual, covers cosine similarity.
- ❓ [Power of Embeddings with Vector Search](https://m.youtube.com/watch?v=Hy38scZx3bg) — Frank Liu (Milvus). Builds semantic text search and reverse image search.

**Reading**
- 🆓 [OpenAI embeddings guide](https://platform.openai.com/docs/guides/embeddings)
- 🆓 `minsearch.VectorSearch` — note its signature: `fit(vectors, payload)`. It does *not* compute embeddings; you bring them. That design tells you where the real work lives.

**Books**
- ⭐ 🆓 **Speech and Language Processing (3rd ed. draft)** — Dan Jurafsky & James H. Martin. [Free chapter PDFs at Stanford](https://web.stanford.edu/~jurafsky/slp3/). Read the **vector semantics and embeddings** chapter — the clearest explanation anywhere of how meaning becomes geometry. (Chapter numbers shift between drafts, so find it by title.)
- 💰 *Hands-On Large Language Models* (above) — dense retrieval and rerankers.

---

## 06 — RAG end to end

**Videos**
- ⭐ ❓ [What is Retrieval-Augmented Generation (RAG)?](https://www.youtube.com/watch?v=T-D1OfcDW1M) — IBM Technology, ~7 min. Slow, clear delivery. Best quick overview of the whole pipeline.
- ❓ [Build Your First RAG Application with LLMs](https://www.youtube.com/watch?v=KSItlTAsMsk) — Alexey Grigorev.
- ❓ [Vector Search RAG Tutorial](https://www.youtube.com/watch?v=JEBDfGqrAUA) — freeCodeCamp, ~1 hr. Concepts then a working build. Save for a code-along session.
- ❓ [RAG Explained in 20 Minutes](https://www.youtube.com/watch?v=RosLeHGBLoY)
- ❓ [RAG Tutorial for Beginners](https://www.youtube.com/watch?v=NaxUrW0yg_M)

**Reading**
- 🆓 [*Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks*](https://arxiv.org/abs/2005.11401) — Lewis et al., 2020. The paper that named RAG. Short and readable once the concept is familiar.

**Books**
- ⭐ 💰 *AI Engineering* — Chip Huyen, ch. 6. The best treatment of RAG in book form.

---

## 07 — Agents and tool calling

**Videos**
- ⭐ ❓ [Agentic RAG Tutorial: Building AI Agents with Function Calling and RAG](https://www.youtube.com/watch?v=GH3lrOsU3AU)

**Reading**
- 🆓 [OpenAI function calling guide](https://platform.openai.com/docs/guides/function-calling)
- 🆓 [Claude tool use guide](https://docs.claude.com/en/docs/agents-and-tools/tool-use/overview)
- 🆓 [Model Context Protocol](https://modelcontextprotocol.io) — the emerging standard for giving models tools.

**Books**
- 💰 *AI Engineering* — Chip Huyen, ch. 6 (Agents half).

---

## 08 — Evaluation and testing

**Reading**
- 🆓 [Evidently docs](https://docs.evidentlyai.com) — LLM evaluation. Fitting, since this repo indexes their documentation.

**Books**
- 💰 *AI Engineering* — Chip Huyen. The evaluation chapters are the strongest part of the book, and the part most often skipped by tutorials.
- 💰 **Designing Machine Learning Systems** — Chip Huyen, O'Reilly, 2022. Pre-LLM, but monitoring and failure modes carry over.

---

## 09 — Tooling

**Reading**
- 🆓 [`uv` documentation](https://docs.astral.sh/uv/) — this project is uv-managed; `uv sync`, `uv add`, `uv run`.
- 🆓 [JupyterLab docs](https://jupyterlab.readthedocs.io/)
- 🆓 [Pro Git](https://git-scm.com/book/en/v2) — free, complete, the standard reference.

---

## Books at a glance

| Book | Author | Cost | Use it for |
|---|---|---|---|
| Introduction to Information Retrieval | Manning, Raghavan, Schütze | 🆓 [free](https://nlp.stanford.edu/IR-book/) | Inverted indexes, TF-IDF, ranking (§03) |
| Speech and Language Processing, 3e draft | Jurafsky & Martin | 🆓 [free](https://web.stanford.edu/~jurafsky/slp3/) | Embeddings, vector semantics (§05) |
| AI Engineering | Chip Huyen | 💰 | The whole course. Start here if buying one |
| Hands-On Large Language Models | Alammar & Grootendorst | 💰 | Gentler, visual; semantic search |
| Relevant Search | Turnbull & Berryman | 💰 | Tuning relevance, field boosting |
| Designing Data-Intensive Applications | Kleppmann | 💰 | B-trees, storage engines (adjacent) |
| Designing Machine Learning Systems | Chip Huyen | 💰 | Production ML, monitoring |

**Cheapest serious path:** the two free books cover §03 and §05 — the exact two concepts this
notebook turns on — at a depth no video reaches. Add Huyen's *AI Engineering* for everything else.
