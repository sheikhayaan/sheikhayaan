<picture>
  <source media="(prefers-color-scheme: dark)" srcset="./assets/dark.svg">
  <source media="(prefers-color-scheme: light)" srcset="./assets/light.svg">
  <img src="./assets/light.svg" width="100%"
       alt="Mohd Ayaan — software engineer, backend and AI infrastructure. ASCII portrait, tech stack, six merged open-source pull requests into ArviZ and Zarr, and six featured projects.">
</picture>

**Mohd Ayaan** · B.Tech Mathematics & Computing, Delhi Technological University · 2024–2028

I work where **numerical correctness** meets **systems engineering** — Bayesian diagnostics where a wrong `rhat` silently corrupts someone's research, and agent pipelines where an unsupervised write is a security incident. Six of my pull requests are merged upstream in the ArviZ and Zarr scientific-Python stack, which is where most of that judgment comes from.

[Portfolio](https://personal-portfolio-compiq.vercel.app) &nbsp;·&nbsp; [LinkedIn](https://linkedin.com/in/mohd-ayaan-7731b4381) &nbsp;·&nbsp; [sheikhayaan408@gmail.com](mailto:sheikhayaan408@gmail.com) &nbsp;·&nbsp; [LeetCode](https://leetcode.com/u/sheikhayaan/)

---

## Selected work

**[AutoPR](https://github.com/sheikhayaan/AUTOPR)** — a pull-request reviewer and CI repairer that is not allowed to write unsupervised.<br>
`Python` `FastAPI` `LangGraph` `Redis Streams` `Postgres` `Docker` · self-hosted; no hosted demo, because it is only useful with write credentials

**Problem.** Reviewing diffs and babysitting flaky CI is high-volume, pattern-heavy work a model is good at — and exactly what must not run unsupervised, since a wrong automated commit has no bounded blast radius.
**Decision.** Only a ≤ low-risk *comment* can post itself. Every code change is queued `PENDING` and applied only through a bearer-authenticated approval call; no agent, risk score, or config flag bypasses it. Dry-run ships as the default — [the full execution path is below](#case-study-autopr).

**[DocMind AI](https://github.com/sheikhayaan/DOCMIND-AI)** — answers questions about a PDF with citations back to the passage.<br>
`Next.js` `TypeScript` `FastAPI` `LangChain` `Pinecone` · [live demo](https://docmind-ai-dusky.vercel.app)

**Problem.** A long document plus a chatbot that guesses is worse than no chatbot, because a fluent wrong answer costs more time than reading.
**Decision.** Chunks are embedded locally on CPU with `all-MiniLM-L6-v2` (384-d) rather than through a hosted embedding API — ingestion costs nothing per document and the ingest path never depends on the chat provider. Answers cite the retrieved chunk they came from.

**[CompIQ](https://github.com/sheikhayaan/compiq)** — compares offers by *level*, not job title: Google L5 = Meta E5 = Amazon L6.<br>
`Next.js` `TypeScript` `Prisma` `Postgres` `NextAuth` · [live demo](https://compiq-three.vercel.app)

**Problem.** Salary sites compare by title, but titles don't translate across companies, so the same seniority looks like two different jobs.
**Decision.** Level equivalence lives in the schema, not in branching code — a `LevelMap` normalizes each company's raw levels onto one ordered scale, so adding a company is an insert rather than a code change, and "L5 = E5" stays auditable.

**[Hybrid Data Pipeline](https://github.com/sheikhayaan/Hybrid-Data-Pipeline)** — a C++ data engine feeding a Python ML workflow across a file boundary.<br>
`C++` `Python` `Pandas` `scikit-learn` `Streamlit` `Docker` · runs from the repo or image; no hosted demo

**Problem.** Cross-language pipelines usually rot at the seam, where two runtimes disagree about the data between them.
**Decision.** The CSV is the only contract — neither language calls into the other, so either half runs, tests, and gets replaced alone. Same seed in, same file out, same metrics.

**[PrepAI](https://github.com/sheikhayaan/PrepAi)** — AI mock interviews across 8 tracks, scored against per-track rubrics.<br>
`React` `Node` `Express` `MongoDB` `BullMQ` `Redis` · self-hosted; needs provider keys

**Problem.** Adaptive interview scoring is slow work, and slow work inside an HTTP request is how a live interview ends in a timeout.
**Decision.** Evaluation is a queued job, not request work: answers stream over SSE while a BullMQ worker scores in the background, so a long evaluation never holds a connection open.

**[Suhana AI](https://github.com/sheikhayaan/Suhana-Ai)** — a Flask studio for script generation, reel rendering, and file tooling.<br>
`Flask` `SQLite` `ffmpeg` `Docker` · self-hosted; no hosted demo

**Problem.** Script generation, video rendering, and PDF/image utilities are normally five separate subscriptions with five billing relationships.
**Decision.** Missing provider keys degrade to a template instead of failing the request, and user-supplied keys are stored behind an encryption wrapper — so the app demonstrates itself without a shared secret in the repo.


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


## Case study: AutoPR

The interesting part of an LLM pipeline isn't the prompts. It's everything around a model that is slow, non-deterministic, and occasionally wrong, because that is what decides whether the thing is safe to point at a real repository.

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="./assets/pipeline-dark.svg">
  <source media="(prefers-color-scheme: light)" srcset="./assets/pipeline-light.svg">
  <img src="./assets/pipeline-light.svg" width="100%"
       alt="Flowchart of the AutoPR execution path: a signed GitHub webhook reaches FastAPI ingress, is enqueued on Redis Streams, processed by a worker, then routed by risk either to an auto-comment or to a human approval gate.">
</picture>

<sub>A signed webhook is enqueued, not processed inline; the router can auto-post a low-risk comment, but the only path to a real write is a human approving the queued decision.</sub>

- **Ingress does no model work.** Every delivery is HMAC-verified in constant time before anything is parsed, and the app refuses to boot on the shipped placeholder secret. The job is persisted idempotently and the request returns `200` immediately.
- **The queue is a Redis Stream, not a `BackgroundTasks`.** A consumer group with `XAUTOCLAIM` reclaims a dead worker's un-acked entries, and a startup reconcile re-enqueues rows still marked `PENDING`. A `job_results` ledger keyed by `job_id` (`INSERT … ON CONFLICT DO NOTHING`) turns at-least-once delivery into effectively-once processing.
- **Two agent tracks, one router.** `code_reviewer → test_gen` handles pull requests; `ci_monitor → fix_agent → fix_verifier` handles failing checks. `fix_verifier` applies the proposed patch to a snapshot inside a Docker sandbox (`--network none`, dropped capabilities, resource caps) and re-runs the failing check before the patch is ever shown to a human.
- **Risk lowers autonomy, never raises it.** The router ranks each outcome; ≤ low risk may auto-post a *comment*, still dry-run by default. Any code change is a `PENDING` decision regardless of its score, and an unparseable risk score fails closed to the human queue.
- **Failure is bounded and observable.** Attempts increment per job; past `AUTOPR_MAX_ATTEMPTS` the job is parked `DEAD` with its last error rather than retried forever. Approvals emit an audit line with actor, decision, action, and outcome.
- **Reproducibility is structural.** The model boundary is the agent layer, the sandbox is disposable, and the React dashboard reads the same ledger the worker writes — so what you see in the queue is what actually happened.

Architecture notes and the ADRs behind these trade-offs live in [`docs/architecture.md`](https://github.com/sheikhayaan/AUTOPR/blob/main/docs/architecture.md) and [`docs/decisions/`](https://github.com/sheikhayaan/AUTOPR/tree/main/docs/decisions).


## Experience

**SDE Intern** · ITJOBXS — Jobs & Education Platform · *Jun 2026 – Jul 2026*
Built full-stack web applications and an AI microservice pipeline. Delivered a MERN e-commerce product for a client end to end; shipped interview-experience and user-verification flows; hardened registration with Google reCAPTCHA.

**Cybersecurity Virtual Intern** · C-DAC Noida (Govt. of India) · *Jul 2025 – Aug 2025*
Audited network topologies, tested system access controls, and ran vulnerability scans against server infrastructure, under the Cyber Gyan Project.


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


## How I build

1. **Safety before autonomy.** An agent that can write code should propose, not commit — and the gate should be one line of API, not a policy paragraph.
2. **Numerical code is guilty until proven correct.** Edge cases in statistics libraries don't throw; they quietly return the wrong number.
3. **Write the error message you wish you'd received.** A renamed argument should tell you the new name, not that your keyword is unexpected.
4. **Reproducibility is a feature.** Seed the RNG, pin the deps, make the pipeline re-runnable.
5. **Small diffs, honest tests.** A change a reviewer can hold in their head gets reviewed properly, and the test that would have caught the bug ships with the fix.

---

<sub>B.Tech Mathematics & Computing · Delhi Technological University · 2024–2028
Open to SDE and backend / infrastructure internship opportunities.</sub>
