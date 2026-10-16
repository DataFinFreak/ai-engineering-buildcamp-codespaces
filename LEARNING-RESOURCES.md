# Learning resources

Resources for the stages **already covered** in this repo — nothing further ahead.
A new stage section gets added only once the work for it is actually done.

**Books are the exception.** A book spans the whole course, so there's no way to list "just the
covered part" of one. The full list sits at the bottom with chapter pointers, and the ⭐ marks the
one that matches where we are now.

**Marks:** ⭐ start here · 🆓 free in full · 💰 paid · ❓ link exists, not personally watched

**Accent tip:** for Alexey's videos, turn on YouTube captions and set playback to 0.75x–0.9x.

---

## Progress

| # | Stage | Where | Status |
|---|---|---|---|
| 1 | Calling the OpenAI API | `01-foundation/02-rag/openai.ipynb` | ✅ covered |
| 2 | Importing data | `01-foundation/02-rag/rag.ipynb`, cells 1–40 | ✅ covered |
| 3 | Search and indexing | `01-foundation/02-rag/rag.ipynb`, cells 41–49 | ✅ covered |

---

## Stage 1 — Calling the OpenAI API

`01-foundation/02-rag/openai.ipynb`

**What this stage covered:** loading the key from `.env`; `responses.create` and the
`output_text` shortcut; sending a `role`/`content` message list instead of a plain string;
reading `usage` for token counts; streaming with `stream=True`; `system` instructions as standing
orders; and the fact that the model keeps no memory between calls — you resend the whole
conversation yourself by appending `response.output` back onto `messages`.

**Docs** — better than any video for this stage, since the API changes faster than tutorials.
- 🆓 ⭐ [OpenAI Responses API reference](https://platform.openai.com/docs/api-reference/responses) — the exact endpoint the notebook calls.
- 🆓 [Text generation guide](https://platform.openai.com/docs/guides/text) — roles, system instructions, message lists.
- 🆓 [Streaming guide](https://platform.openai.com/docs/guides/streaming-responses) — what the `delta` events in the notebook's loop are.
- 🆓 [Pricing](https://openai.com/api/pricing/) — pair it with the `usage` numbers you printed to see what a call actually costs.
- 🆓 [`openai-python` SDK on GitHub](https://github.com/openai/openai-python) — the README covers the client, errors and retries.

**Videos**
- ⭐ ❓ [Chat with Your Own Data: Introduction to the LLM Zoomcamp](https://www.youtube.com/watch?v=q-p36Ak6YI8) — Alexey Grigorev. The frame for the whole course.
- ❓ [OpenAI with Python: A Step-by-Step Guide for Beginners](https://www.youtube.com/watch?v=-A7njXsJl5M) — setup through first calls.
- ❓ [OpenAI Responses API Tutorial (Python)](https://www.youtube.com/playlist?list=PL4HikwTaYE0GgR36Px9iXb9bNgrLihCnG) — playlist on the Responses API specifically, which is the newer endpoint most tutorials still don't use.

---

## Stage 2 — Importing data

`01-foundation/02-rag/rag.ipynb`, cells 1–40

**What this stage covered:** fetching a GitHub branch as a zip over plain HTTP; opening it with
`BytesIO` so nothing touches disk; pulling one file out first before looping; splitting YAML
frontmatter from body text with `frontmatter.loads()`; trimming the `docs-main/` prefix off paths;
shaping one dict of `content` / `title` / `description` / `filename`; running that over the whole
repo while skipping non-`.md`/`.mdx` files; then replacing the entire loop with
`gitsource.GithubRepositoryDataReader` plus `.parse()`.

**Docs**
- 🆓 ⭐ [`python-frontmatter`](https://python-frontmatter.readthedocs.io/) — `loads()` returning `.metadata` and `.content`, which is the one idea doing real work in this stage.
- 🆓 [`zipfile`](https://docs.python.org/3/library/zipfile.html) + [`io.BytesIO`](https://docs.python.org/3/library/io.html#io.BytesIO) — reading an archive straight from bytes in memory.
- 🆓 [`requests` quickstart](https://requests.readthedocs.io/en/latest/user/quickstart/) — `allow_redirects` matters here, since GitHub redirects the zip URL.
- ❓ [`gitsource` on PyPI](https://pypi.org/project/gitsource/) — the library that collapses cells 1–28 into three lines.
- 🆓 [Evidently docs](https://docs.evidentlyai.com) — the corpus you downloaded. Skim it so search results mean something.

**Videos**

None worth your time. This stage is file plumbing — zip handling, string splitting, dict building.
The library docs above are faster and more accurate than any tutorial, and nothing here is
conceptually hard enough to need one.

---

## Stage 3 — Search and indexing ⬅ *current*

`01-foundation/02-rag/rag.ipynb`, cells 41–49

**What this stage covered:** `minsearch.Index` with `text_fields=["title","description","content"]`
and `keyword_fields=["filename"]`; `fit(documents)` building the index; `search(query)` returning
ranked documents for `'LLM as a Judge'`.

**First, the distinction that saves an hour.** "Indexing" means two unrelated things:

| | Database indexing | Search indexing |
|---|---|---|
| **Question it answers** | `WHERE user_id = 42` | "find pages about LLM as a judge" |
| **Structure** | B-tree / B+tree | Inverted index, TF-IDF matrix |
| **Result** | Exact match, or nothing | Everything, ranked by relevance |
| **Used by** | Postgres, MySQL | Elasticsearch, **minsearch** |

Both precompute a lookup structure so you don't rescan everything — that shared idea is why they
share a name. **This stage is the right-hand column.** B-tree videos are decent general knowledge
but will not explain this code.

**Videos**
- ⭐ ❓ [Implement a Search Engine](https://www.youtube.com/watch?v=nMrGK5QgPVE) — Alexey Grigorev. The workshop `minsearch` came out of; he builds it from scratch. Nothing else maps this directly onto the notebook.
- ❓ [Database Indexing Explained: B-Tree vs Full-Text Index](https://www.youtube.com/watch?v=_pKudLsENYg) — covers both senses and contrasts them. Use it if the table above didn't land.
- ❓ [Database Indexing Explained](https://www.youtube.com/watch?v=k1eyF7lEO1U) — the general "why indexes exist" idea.

**Docs**
- 🆓 ⭐ [`minsearch` source](https://github.com/alexeygrigorev/minsearch) — ~200 lines; `fit()` and `search()` are the whole library. Also local at `.venv/Lib/site-packages/minsearch/`.
- 🆓 [scikit-learn: text feature extraction](https://scikit-learn.org/stable/modules/feature_extraction.html#text-feature-extraction) — the TF-IDF weighting formula. minsearch is a thin wrapper over this.
- 🆓 [`TfidfVectorizer` reference](https://scikit-learn.org/stable/modules/generated/sklearn.feature_extraction.text.TfidfVectorizer.html) — every knob `Index` passes through.

**Two things to try next in this notebook**
- `boost_dict={"title": 3, "description": 2, "content": 1}` — right now all three fields count equally, so a passing mention in a long page competes with a page actually titled for your query.
- `filter_dict={"filename": ...}` — what the `keyword_fields` declaration was for.

---

## Not covered yet

Listed so the order is visible. **No resources here on purpose** — each gets its own section when
we actually do the work.

- Chunking
- Embeddings and vector search
- RAG end to end (retrieval feeding generation)
- Agents and tool calling
- Evaluation and testing

---

## Books

Exempt from the stage rule, since a book can't be split by stage. Chapter pointers show which
part applies to where we are now.

- ⭐ 🆓 **Introduction to Information Retrieval** — Manning, Raghavan & Schütze, Cambridge University Press, 2008. [Free full text](https://nlp.stanford.edu/IR-book/). **Chapter 1** (inverted index) and **Chapter 6** (term weighting, TF-IDF, vector space model) are stage 3 exactly, at a depth no video reaches. Pre-dates LLMs, which is fine — this layer hasn't changed.
- 💰 **AI Engineering: Building Applications with Foundation Models** — Chip Huyen, O'Reilly, 2025. The one book matching this whole course; chapter 6 is "RAG and Agents". Buy it when you reach the later stages, not now.
- 💰 **Hands-On Large Language Models** — Jay Alammar & Maarten Grootendorst, O'Reilly, Sept 2024. Gentler and more visual; strong on semantic search. Useful from the embeddings stage onward.
- 💰 **Relevant Search** — Doug Turnbull & John Berryman, Manning Publications, 2016. Entirely about tuning relevance — field boosting, why your top hit is wrong. Relevant the moment you try `boost_dict`.
- 💰 **Designing Data-Intensive Applications** — Martin Kleppmann, O'Reilly. Chapter 3, "Storage and Retrieval", is the best printed explanation of B-trees. That's the *database* column of the table above — career-valuable, not course-required.
