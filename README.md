# Editorial Research Assistant — RAG Fix Task

## Task Overview

A digital media company runs a RAG-powered research assistant that helps journalists surface relevant article history and background context. The assistant ingests article chunks nightly into a pgvector database and answers questions through a FastAPI service. Currently, when the system returns wrong or hallucinated answers, engineers have no way to determine whether the fault lies in retrieval (wrong chunks returned) or generation (LLM ignoring good chunks) — there's no visibility into what happened during a request. A diagnostic endpoint that should help investigate issues is unacceptably slow under realistic data volumes. Additionally, the team has an evaluation dataset but no way to measure retrieval quality, so regressions go undetected.

## Objectives

- Answering a research question produces enough diagnostic information, after the fact, to determine whether a bad answer was caused by retrieval (wrong chunks) or generation (LLM ignoring good chunks).
- The diagnostic endpoint responds in under 500 ms for a request with up to 20 associated chunks, and returns results in manageable pages rather than all at once.
- The evaluation script reports retrieval-quality metrics (recall@5 and MRR) for the current retriever against the provided query dataset.
- When the retriever cannot find sufficiently relevant chunks for a question, the system returns a safe fallback response instead of forcing an answer from irrelevant context.
- All code follows production standards: typed function signatures, structured exception handling, and no secrets in source files.

## Helpful Tips

- Think about what a support engineer would need to see, after the fact, to tell a retrieval failure from a generation failure for a single request — and where in the request lifecycle that information would need to be captured.
- If an endpoint is slow, profile it before you guess — don't assume the bottleneck without confirming where the time is actually going.
- Look up how recall@k and MRR are conventionally defined in information retrieval, and how you'd compute them from a set of questions with known relevant sources.
- Consider what a RAG system should do when it genuinely doesn't have relevant information to answer with, versus forcing an answer from whatever it retrieved.

## How to Verify

- Ask a research question and confirm the application logs contain an entry for that request that would let an engineer tell whether the time went to retrieval or generation, and which chunks were used.
- Look up diagnostics for a prior request and confirm the response arrives quickly and is returned in manageable pages.
- Run the evaluation script and confirm it reports recall@5 and MRR without errors, even if scores are low with the sample data.
- Ask a question on a topic not covered by any ingested article and confirm the response contains a fallback message rather than an invented answer.
- Run the test suite and confirm all tests pass.
