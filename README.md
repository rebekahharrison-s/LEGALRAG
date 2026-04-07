## Legal RAG Assistant — UK Law Access for Litigants in PersonProject: Leveraging Large Language Models for Legal Case Analysis
Type: Experiment-Based MSc Dissertation

Here's a high-level overview of your thesis project, ready for your GitHub README:


Overview
This project addresses the access-to-justice gap in the UK by building a Retrieval-Augmented Generation (RAG) assistant that helps non-lawyers — particularly litigants in person (LiPs) — understand their legal rights using plain English.
The system ingests open-access UK legal texts, retrieves the most relevant passages in response to a user's scenario, and generates concise, citation-anchored answers using a locally-run language model. It is explicitly designed as a research and education tool, not a substitute for legal advice.

Key Features

Multi-source UK legal corpus — statutes from legislation.gov.uk, case law from BAILII, National Archives, and UK Supreme Court PDFs, plus guidance from Citizens Advice
Semantic retrieval — sentence-transformer embeddings (all-mpnet-base-v2) with exact cosine similarity search, plus an optional cross-encoder reranker
Domain tagging — lightweight keyword-based classifier for Contract, Tort, Employment, Consumer, Property, Mixed, and Unknown domains
Plain-English answer generation — FLAN-T5-Large with constrained prompting (citations required, no definitive legal advice, admits uncertainty)
Gradio web UI — accepts free-text scenarios, returns an answer + top-k supporting passages with titles and source links
Rigorous evaluation — Recall@6, MRR@6, nDCG@6 on a hand-crafted query set (Recall@6 = 0.95, MRR@6 = 0.845)


Tech Stack
ComponentToolEmbeddingssentence-transformers/all-mpnet-base-v2Rerankercross-encoder/ms-marco-MiniLM-L-6-v2Generatorgoogle/flan-t5-largeVector searchExact cosine (NumPy); FAISS trialled but not retainedUIGradioDatapandas, PyMuPDF, BeautifulSoup, ElementTree

Pipeline
Open UK sources (statutes, judgments, guidance)
    → Clean / deduplicate / normalise
    → Domain tagging (heuristic keyword scoring)
    → Chunk (750 tokens, 150 overlap)
    → Embed (sentence-transformers)
    → Exact cosine retrieval + optional rerank
    → FLAN-T5-Large generation
    → Gradio UI with answer + citations

Design Decisions

Exact search over FAISS — at this corpus scale, FAISS adds overhead without meaningful latency gain, and introduces dependency fragility
No fine-tuning — relies on strong off-the-shelf encoders with careful preprocessing
Conservative prompting — outputs are grounded in retrieved passages, hedged, and always disclaim that this is not legal advice
Domain filter is optional — avoids hiding useful out-of-domain passages for borderline queries


Ethical Considerations

All data sourced from open UK repositories only
No PII collected or stored
Prominent disclaimers throughout the UI
High-risk topics (criminal, eviction, immigration) trigger escalation prompts to seek qualified legal advice
Bias mitigations documented; retrieval metrics reported by domain


Limitations & Future Work

Corpus size is modest; some sources blocked intermittently
Domain tagger is heuristic — a learned classifier would improve precision
Automatic citation-consistency checking not yet implemented
FAISS or a managed ANN service warranted if corpus exceeds ~50–100k chunks
