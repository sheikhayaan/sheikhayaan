<picture>
  <source media="(prefers-color-scheme: dark)" srcset="./assets/dark.svg">
  <source media="(prefers-color-scheme: light)" srcset="./assets/light.svg">
  <img src="./assets/light.svg" width="100%"
       alt="Mohd Ayaan — software engineer, backend and AI infrastructure. A five-layer stack map, and four counts: six merged pull requests, three upstream repositories, six systems shipped, two live demos.">
</picture>

**Mohd Ayaan** · B.Tech Mathematics & Computing, Delhi Technological University · 2024–2028

I work where **numerical correctness** meets **systems engineering** — Bayesian diagnostics where a wrong `rhat` silently corrupts someone's research, and agent pipelines where an unsupervised write is a security incident. Six of my pull requests are merged upstream in the ArviZ and Zarr scientific-Python stack, which is where most of that judgment comes from.

[Portfolio](https://personal-portfolio-compiq.vercel.app) &nbsp;·&nbsp; [LinkedIn](https://linkedin.com/in/mohd-ayaan-7731b4381) &nbsp;·&nbsp; [sheikhayaan408@gmail.com](mailto:sheikhayaan408@gmail.com) &nbsp;·&nbsp; [LeetCode](https://leetcode.com/u/sheikhayaan/)

---

## What's here

| Project | One line | Status |
| :-- | :-- | :-- |
| [AutoPR](https://github.com/sheikhayaan/AUTOPR) | Pull-request reviewer and CI repairer that is not allowed to write unsupervised | self-hosted · MIT |
| [DocMind AI](https://github.com/sheikhayaan/DOCMIND-AI) | Answers questions about a PDF and shows the passage it came from | [live](https://docmind-ai-dusky.vercel.app) |
| [CompIQ](https://github.com/sheikhayaan/compiq) | Compares offers by level, not job title | [live](https://compiq-three.vercel.app) |
| [Hybrid Data Pipeline](https://github.com/sheikhayaan/Hybrid-Data-Pipeline) | C++ data engine feeding a Python ML workflow across a file boundary | self-hosted · MIT |
| [PrepAI](https://github.com/sheikhayaan/PrepAi) | AI mock interviews across 8 tracks, scored against per-track rubrics | self-hosted |
| [Suhana AI](https://github.com/sheikhayaan/Suhana-Ai) | Flask studio for script generation, reel rendering and file tooling | self-hosted |

Each one below is written as a case study: the problem, what was actually built, the decision that mattered, and the limit I'd name first.

---

## Case study — AutoPR

`Python` `FastAPI` `LangGraph` `Redis Streams` `Postgres` `Docker` · MIT · no hosted demo, because it is only useful with write credentials

**Problem.** Reviewing diffs and babysitting flaky CI is high-volume, pattern-heavy work a model is good at — and exactly what must not run unsupervised, since a wrong automated commit has no bounded blast radius.

**Built.** A FastAPI ingress that verifies the GitHub HMAC and enqueues; a Redis Streams consumer group; a LangGraph worker with two tracks, `code_reviewer → test_gen` for pull requests and `ci_monitor → fix_agent → fix_verifier` for failing checks; a risk router; a disposable Docker sandbox that replays a patch with `--network none`; and a React dashboard that reads the same ledger the worker writes.

**Decision that mattered.** Risk lowers autonomy, never raises it. A ≤ low-risk finding may auto-post a *comment*, still dry-run by default. Any code change is a `PENDING` decision whatever its score, and an unparseable risk score fails closed to the human queue. The gate is one bearer-authenticated API call, not a policy paragraph that a future refactor can quietly drop.

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="./assets/pipeline-dark.svg">
  <source media="(prefers-color-scheme: light)" srcset="./assets/pipeline-light.svg">
  <img src="./assets/pipeline-light.svg" width="100%"
       alt="Flowchart of the AutoPR execution path: a signed GitHub webhook reaches FastAPI ingress, is enqueued on Redis Streams, processed by a worker, then routed by risk either to an auto-comment or to a human approval gate.">
</picture>

<sub>A signed webhook is enqueued, not processed inline. The router can post a low-risk comment by itself; the only path to a real write is a human approving the queued decision.</sub>

Ingress does no model work. Every delivery is HMAC-verified in constant time before anything is parsed, and the app refuses to boot on the shipped placeholder secret. The queue is a Redis Stream rather than `BackgroundTasks`, so a consumer group with `XAUTOCLAIM` reclaims a dead worker's un-acked entries and a startup reconcile re-enqueues rows still marked `PENDING`. A `job_results` ledger keyed by `job_id` (`INSERT … ON CONFLICT DO NOTHING`) turns at-least-once delivery into effectively-once processing. Attempts increment per job and past `AUTOPR_MAX_ATTEMPTS` the job is parked `DEAD` with its last error instead of retried forever.

**Limit I'd name first.** It has never been pointed at a repository with real traffic. The retry, dead-letter and approval paths are exercised by tests and by a sandbox that only ever sees synthetic patches.

Architecture notes and the thirteen ADRs behind these trade-offs: [`docs/architecture.md`](https://github.com/sheikhayaan/AUTOPR/blob/main/docs/architecture.md), [`docs/decisions/`](https://github.com/sheikhayaan/AUTOPR/tree/main/docs/decisions).

---

## Case study — DocMind AI

`Next.js` `TypeScript` `FastAPI` `LangChain` `Pinecone` · [live demo](https://docmind-ai-dusky.vercel.app)

**Problem.** A long document plus a chatbot that guesses is worse than no chatbot, because a fluent wrong answer costs more time than reading the section yourself.

**Built.** A two-runtime app: FastAPI for ingest and query, Next.js for the interface. Ingestion chunks a PDF, embeds it on CPU with `all-MiniLM-L6-v2` (384-d), and upserts to Pinecone. Query rewrites the question, runs vector search (`k=8`) and a keyword pass in parallel, fuses them with reciprocal-rank fusion (`k=60`), optionally reranks the top of that list with a cross-encoder, and hands four chunks to the model.

**Decision that mattered.** Three separate guards against confident nonsense instead of one clever prompt. Embeddings run locally, so ingestion costs nothing per document and never depends on the chat provider. The rewrite step rejects its own output when it looks like SQL or Boolean syntax rather than search text. The system prompt is allowed exactly one escape hatch — "I could not find this information in the document" — and the UI shows the retrieved passage next to the answer so a reader can check it in one glance. `evaluate.py` scores the pipeline with a judge model so retrieval changes are measured rather than felt.

**Limit I'd name first.** The cross-encoder reranker is off unless `ENABLE_RERANKER` is set, because it adds a model download and a second of latency to a free-tier box. Without it the fusion order is used, which is worse and I'd rather say so.

---

## Case study — CompIQ

`Next.js` `TypeScript` `Prisma` `Postgres` `NextAuth` · [live demo](https://compiq-three.vercel.app)

**Problem.** Salary sites compare by title, but titles don't translate across companies, so the same seniority looks like two different jobs — and "average compensation" hides the shape of the distribution that actually matters when you are negotiating.

**Built.** A Next.js App Router app over Postgres via Prisma, with NextAuth for sessions, 15 companies seeded to 360 salary rows across countries, per-company pages, a compare view, a level-equivalence view, and a submission flow. A separate FastAPI service predicts base, bonus, equity and total compensation from role, level, company, location and years of experience.

**Decision that mattered.** Normalisation is a pure function over one explicit table, and every salary row stores its `normalizedLevel` and `levelOrder` at write time — so `L5`, `E5`, `ICT4` and `62` land on a single ordered scale, reads never re-derive it, and correcting a mapping is a data migration rather than a change to every query. Stats are median and percentile, never mean: compensation distributions have a long right tail, and a mean would let one outlier reprice a whole level.

**Limit I'd name first.** The deployed demo renders from a checked-in dataset so it stays up without a database; the Prisma API routes behind it serve the same schema. Live user submissions are not wired to the public deployment.

---

## Case study — Hybrid Data Pipeline

`C++` `Python` `Pandas` `scikit-learn` `Streamlit` `Docker` · MIT · runs from the repo or the image; no hosted demo

**Problem.** Cross-language pipelines usually rot at the seam, where two runtimes quietly disagree about the data between them. And a model pipeline that cannot be re-run from scratch is a result, not a system.

**Built.** A C++17 engine that models housing records and writes them with a seeded `std::mt19937`, a CSV boundary, and a Python layer that cleans with Pandas, splits, trains a `RandomForestRegressor` (200 trees, depth 8) and reports MAE and R². Streamlit sits on top for interaction; Docker and a GitHub Actions job compile the C++ and run the whole chain so the seam is tested on every push.

**Decision that mattered.** The CSV is the only contract. Neither language calls into the other, so either half can be run, tested, replaced or rewritten alone — and because the generator is seeded, the same seed produces the same file, which makes the metrics comparable across commits instead of a fresh lottery each run.

**Limit I'd name first.** The data is synthetic, so the R² is a statement about the pipeline being wired correctly, not about Gurgaon property prices.

---

## Case study — PrepAI

`React` `Node` `Express` `MongoDB` `BullMQ` `Redis` · self-hosted; needs provider keys

**Problem.** Adaptive interview scoring is slow work — a rubric-graded evaluation of a whole session — and slow work inside an HTTP request is how a live interview ends in a gateway timeout.

**Built.** A React/Vite client and an Express/MongoDB API. Eight interview tracks, each with its own non-leaking rubric, from DSA and system design through consulting case study, PM, sales and engineering management. Answers stream token-by-token over SSE while the session is scored, and the final evaluation is written back to the session document with per-dimension scores, an overall band and a seven-day plan.

**Decision that mattered.** Scoring is a queued job, not request work. A BullMQ queue with three attempts and exponential backoff runs the grader, and when Redis is absent the same function is invoked in-process via `setImmediate` — so local development works without a broker while production still gets retries and a concurrency cap. Two cost controls sit in front of the model: a TTL/LRU cache keyed on the interview configuration (`sessionType:companyTier:targetCompany:seniorityLevel`, 200 entries, one hour) so identical setups don't re-buy the same opening question, and per-route rate limits on auth, session start and answer submission.

**Limit I'd name first.** There is no hosted demo, because every session costs provider tokens. The repo ships test scripts (end-to-end, adversarial, production audit) that you run against your own key.

---

## Case study — Suhana AI

`Flask` `SQLite` `ffmpeg` `Docker` · self-hosted; no hosted demo

**Problem.** Script generation, reel rendering, and PDF/image utilities are normally five subscriptions with five billing relationships, and the whole thing is unbuildable if the model provider is down or the user has no key.

**Built.** A Flask studio on SQLAlchemy/SQLite: email and OAuth login, script generation across several providers, image generation, a script-to-reel flow that renders with ffmpeg, PDF merge and image editing, a credit ledger, a BYOK vault, and a background worker that moves jobs through `pending → processing → completed / failed`. `generate_process.py` runs that worker as its own process for anything production-shaped.

**Decision that mattered.** Never fail a request because a provider is missing. Every generation path degrades to a template or a placeholder, and text-to-speech degrades to silent audio of the estimated duration, so the reel pipeline still produces a file you can inspect. User-supplied keys are encrypted at rest behind a wrapper and never echoed back; free usage is capped at three generations per feature with a single guest try, and metering runs off the same rows the feature writes rather than a separate counter that can drift.

**Limit I'd name first.** It is a single-tenant monolith: one SQLite file, one process, a 64 MB upload cap. That is the right shape for a studio MVP and the wrong shape for the same product with customers.

---

## Open source

Six merged pull requests in the scientific Python stack — the work I'd point at first, because other people's research depends on these libraries. All six are merged, between August and October 2026; every link goes to the upstream discussion.

| PR | What changed | Upstream |
| :-- | :-- | :-- |
| [#485](https://github.com/arviz-devs/arviz-stats/pull/485) | `r2` gained an `axis` argument and a DataArray interface, so it composes with the labelled API instead of only raw arrays | `arviz-stats` |
| [#474](https://github.com/arviz-devs/arviz-stats/pull/474) | `rhat_nested` over-counted superchains, because `np.bincount` invented zero-counts for absent labels; now `np.unique(..., return_counts=True)` | `arviz-stats` |
| [#399](https://github.com/arviz-devs/arviz-stats/pull/399) | `reloo`'s `k_threshold` defaulted to `-inf` while the documentation promised `None` — the code now matches the docs | `arviz-stats` |
| [#392](https://github.com/arviz-devs/arviz-stats/pull/392) | passing the pre-1.0 `hdi_prob` now raises an error that names `prob` and links the migration guide, instead of a bare `TypeError` | `arviz-stats` |
| [#2601](https://github.com/arviz-devs/arviz/pull/2601) | documented the `hdi_prob` → `prob` rename in ArviZ's migration guide | `arviz` |
| [#4270](https://github.com/zarr-developers/zarr-python/pull/4270) | the async-vs-sync codec test compared raw gzip bytes, and gzip embeds a wall-clock timestamp — the test now skips the byte comparison for `GzipCodec` and keeps the decode assertions | `zarr-python` |

Half of these are fixes where nothing crashed: a superchain count, a default argument, and a byte-for-byte assertion were each wrong only in the value they produced. That failure mode — code that runs fine and answers incorrectly — is the reason this stack is the one I keep coming back to.

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="./assets/signals-dark.svg">
  <source media="(prefers-color-scheme: light)" srcset="./assets/signals-light.svg">
  <img src="./assets/signals-light.svg" width="100%"
       alt="Two panels. Left: a donut chart of 6 merged pull requests — arviz-stats 4, arviz 1, zarr-python 1. Right: horizontal bars of language bytes across 7 public repositories, led by HTML, Python, JavaScript and TypeScript.">
</picture>

<sub>The donut counts merged PRs by upstream project. The bars measure language bytes across every public repo — markup and tooling included, so they show repository bulk rather than effort.</sub>

---

## Experience

**SDE Intern** · ITJOBXS — Jobs & Education Platform · *Jun 2026 – Jul 2026*
Built full-stack web applications and an AI microservice pipeline. Delivered a MERN e-commerce product for a client end to end; shipped interview-experience and user-verification flows; hardened registration with Google reCAPTCHA.

**Cybersecurity Virtual Intern** · C-DAC Noida (Govt. of India) · *Jul 2025 – Aug 2025*
Audited network topologies, tested system access controls, and ran vulnerability scans against server infrastructure, under the Cyber Gyan Project.

---

## Toolbox

| | |
| :-- | :-- |
| **Languages** | Python · TypeScript · JavaScript · C++ · SQL |
| **Backend & APIs** | FastAPI · Flask · Express · SQLAlchemy · Alembic · NextAuth |
| **AI & retrieval** | LangGraph · LangChain · RAG · Pinecone · Groq · Claude · Gemini |
| **Databases & messaging** | Postgres · MongoDB · SQLite · Redis Streams · BullMQ |
| **Frontend** | React · Next.js · Tailwind CSS · Vite |
| **Infrastructure** | Docker · GitHub Actions · Vercel · Render |
| **Foundations** | Probability & Statistics · Numerical Methods · Data Structures & Algorithms |

---

## How I build

1. **Safety before autonomy.** An agent that can write code should propose, not commit — and the gate should be one line of API, not a policy paragraph.
2. **Numerical code is guilty until proven correct.** Edge cases in statistics libraries don't throw; they quietly return the wrong number.
3. **Write the error message you wish you'd received.** A renamed argument should tell you the new name, not that your keyword is unexpected.
4. **Name the limit before someone else does.** Every project above has one, and the honest version is shorter than the defensive one.
5. **Reproducibility is a feature.** Seed the RNG, pin the deps, make the pipeline re-runnable.
6. **Small diffs, honest tests.** A change a reviewer can hold in their head gets reviewed properly, and the test that would have caught the bug ships with the fix.

---

<sub>B.Tech Mathematics & Computing · Delhi Technological University · 2024–2028
Open to SDE and backend / infrastructure internship opportunities.</sub>
