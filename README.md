# Mohd Ayaan

**Software engineer building backend systems, AI infrastructure, and data pipelines.**
B.Tech Mathematics & Computing @ Delhi Technological University · Open source contributor to [ArviZ](https://github.com/arviz-devs) and [Zarr](https://github.com/zarr-developers)

[![Portfolio](https://img.shields.io/badge/Portfolio-visit-6366f1?style=flat-square&logo=vercel&logoColor=white)](https://personal-portfolio-compiq.vercel.app)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-connect-0A66C2?style=flat-square&logo=linkedin&logoColor=white)](https://linkedin.com/in/mohd-ayaan-7731b4381)
[![LeetCode](https://img.shields.io/badge/LeetCode-profile-FFA116?style=flat-square&logo=leetcode&logoColor=white)](https://leetcode.com/u/sheikhayaan/)
[![Email](https://img.shields.io/badge/Email-sheikhayaan408@gmail.com-EA4335?style=flat-square&logo=gmail&logoColor=white)](mailto:sheikhayaan408@gmail.com)

---

I work at the intersection of **numerical correctness** and **systems engineering**. That means Bayesian diagnostics libraries where a wrong `rhat` silently corrupts research, and agent pipelines where an unsupervised write is a security incident.

## Open Source

Six merged pull requests in the scientific Python stack:

| PR | What it does | Project | Status |
| :-- | :-- | :-- | :-- |
| [#485](https://github.com/arviz-devs/arviz-stats/pull/485) | Added `axis` argument and DataArray interface to `r2` functions | arviz-stats | ✅ Merged |
| [#474](https://github.com/arviz-devs/arviz-stats/pull/474) | Fixed superchain counts in `rhat_nested` using `np.unique` | arviz-stats | ✅ Merged |
| [#399](https://github.com/arviz-devs/arviz-stats/pull/399) | Fixed `reloo` k-threshold | arviz-stats | ✅ Merged |
| [#392](https://github.com/arviz-devs/arviz-stats/pull/392) | Added a helpful error when `hdi_prob` is passed instead of `prob` | arviz-stats | ✅ Merged |
| [#2601](https://github.com/arviz-devs/arviz/pull/2601) | Documented the `hdi_prob` → `prob` migration path | arviz | ✅ Merged |
| [#4270](https://github.com/zarr-developers/zarr-python/pull/4270) | Skipped exact byte comparison for the gzip codec (timestamp variance) | zarr-python | ✅ Merged |

> Correctness work in libraries other people's research depends on.

## Featured Work

<table>
<tr>
<td width="50%" valign="top">

### 🔍 [AutoPR](https://github.com/sheikhayaan/AUTOPR)
Multi-agent PR reviewer that repairs failing CI, gated behind human approval.

A LangGraph pipeline reviews diffs, drafts tests, and proposes fixes. Anything that mutates code, or carries non-trivial risk, is **queued for a human** rather than applied.

`Python` `FastAPI` `LangGraph` `Redis Streams` `Postgres` `Alembic` `Docker` `pytest` `CI + release workflows`

</td>
<td width="50%" valign="top">

### 📄 [DocMind AI](https://github.com/sheikhayaan/DOCMIND-AI)
RAG chatbot: upload any PDF, ask questions answered from the document.

Document-grounded answers with citations, backed by a Pinecone vector store.

🔗 **[Live demo](https://docmind-ai-dusky.vercel.app)**

`Next.js` `TypeScript` `FastAPI` `LangChain` `Groq` `HuggingFace` `Pinecone`

</td>
</tr>
<tr>
<td width="50%" valign="top">

### 💰 [CompIQ](https://github.com/sheikhayaan/compiq)
Compensation intelligence that compares offers **by level**, not job title — Google L5 = Meta E5 = Amazon L6.

Side-by-side comp breakdowns, level taxonomy, anonymous submissions with duplicate detection.

🔗 **[Live demo](https://compiq-three.vercel.app)**

`Next.js` `TypeScript` `Prisma` `Tailwind`

</td>
<td width="50%" valign="top">

### ⚙️ [Hybrid Data Pipeline](https://github.com/sheikhayaan/Hybrid-Data-Pipeline)
Cross-language pipeline: a **C++ data engine** generates synthetic housing records over a CSV boundary, feeding a Pandas + scikit-learn training workflow.

Native file I/O, reproducible RNG, struct-based modeling.

`C++` `Python` `scikit-learn` `Docker` `GitHub Actions`

</td>
</tr>
<tr>
<td width="50%" valign="top">

### 🎯 [PrepAI](https://github.com/sheikhayaan/PrepAi)
Full-stack AI technical interview platform with async job processing behind a Redis + BullMQ queue.

`React` `Vite` `Node` `Express` `MongoDB` `Claude` `Tailwind`

</td>
<td width="50%" valign="top">

### ✨ [Suhana AI](https://github.com/sheikhayaan/Suhana-Ai)
Flask-based AI creator studio: script generation, visual studio, reel rendering, PDF and image tooling, credit tracking, and BYOK API keys.

`Flask` `Python` `Docker` `ffmpeg`

</td>
</tr>
</table>

## Experience

**SDE Intern** · ITJOBXS — Jobs & Education Platform · *Jun 2026 – Jul 2026*
Built full-stack web applications and an AI microservice pipeline. Delivered a MERN e-commerce product for a client end to end; shipped interview-experience and user-verification flows; hardened registration with Google reCAPTCHA.
`React` `Node` `Express` `MongoDB` `Python`

**Cybersecurity Virtual Intern** · C-DAC Noida (Govt. of India) · *Jul 2025 – Aug 2025*
Audited network topologies, tested system access controls, and ran vulnerability scans against server infrastructure, under the Cyber Gyan Project.
`Penetration Testing` `Vulnerability Assessment` `Network Security`

## Toolbox

| | |
| :-- | :-- |
| **Languages** | Python · TypeScript · JavaScript · C++ · SQL |
| **Backend** | FastAPI · Flask · Express · SQLAlchemy · Alembic · Redis · Postgres · MongoDB |
| **AI / Data** | LangChain · LangGraph · RAG · vector DBs · Pandas · scikit-learn · NumPy |
| **Frontend** | React · Next.js · Tailwind CSS |
| **Infra** | Docker · GitHub Actions · Fly.io · Render · Vercel |
| **Foundations** | Probability & Statistics · Numerical Methods · Data Structures & Algorithms |

## How I build

1. **Safety before autonomy.** An agent that can write code should propose, not commit. I'd rather queue a fix than corrupt someone's repo.
2. **Numerical code is guilty until proven correct.** Edge cases in statistics libraries don't throw — they quietly return the wrong number.
3. **Write the error message you wish you'd received.** Several of my merged PRs are exactly this.
4. **Reproducibility is a feature.** Seed the RNG, pin the deps, make the pipeline re-runnable.

---

<sub>B.Tech Mathematics & Computing, Delhi Technological University (2024–2028) · Open to SDE and backend/infra internship opportunities</sub>
