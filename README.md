# Tomás Houdely

**Founder & Systems Architect** — autonomous agent systems, applied AI platforms, and trust infrastructure for physical-asset supply chains.

Industrial Systems Engineer with an M.S. in Electrical Engineering & Computer Science from MIT (2025) and more than 13 years automating industrial processes, integrating legacy systems with modern platforms, and building custom enterprise-grade systems.

**Core expertise:** RAG and GraphRAG systems · specialist agent deployment · fine-tuning · AI and machine learning · traceability · cryptography · post-quantum cybersecurity.

I build production systems end to end: distributed backends, multi-agent orchestration layers, knowledge-graph substrates, edge sensing pipelines, and the operator-facing consoles that make them controllable. Most of my work spans several runtimes at once — Python services, Go APIs, Rust desktop shells, TypeScript frontends — deployed as self-hosted, vendor-independent stacks.

---

## Why this catalog exists

The majority of my repositories are **private**, because they contain client work, unfiled intellectual property, and commercially sensitive material. That makes a normal GitHub profile a poor signal of what I actually build.

This page is the public catalog: what each system does, how it is architected, and which technologies it runs on — without exposing source, credentials, or protected IP.

**If you want to see the code, ask.** See [Requesting access](#requesting-access).

---

## Currently live

### [web3-rwa-monitor](https://github.com/Blastoom/web3-rwa-monitor) · Public · [Live dashboard](https://web3-rwa-monitor.pages.dev)

Dashboard tracking the global real-world-asset (RWA) tokenization landscape: total RWA market cap, tokenized treasuries, private credit, bonds, equities and real estate, DeFi TVL, stablecoin composition, and on-chain metrics across the top 15 blockchains.

An ingestion job refreshes the dataset and regenerates a fully static dashboard — no server, no runtime database, no per-visit API cost.

| | |
|---|---|
| **Stack** | Python ingestion pipeline · static HTML/CSS/JS dashboard · Cloudflare Pages |
| **Design** | Static-generation over live querying: deterministic output, zero cold starts, trivially cacheable at the edge |
| **Status** | Publicly deployed — 150 commits. Dataset last refreshed 2026-07-31; scheduled refresh currently paused |

**Live at [web3-rwa-monitor.pages.dev](https://web3-rwa-monitor.pages.dev)** — and this is the one repository in this catalog you can read today. It is representative of how I ship: small surface, no unnecessary infrastructure.

### [CRODAX website](https://crodax.com) · Private source · Live

The public website for CRODAX: industry tracks, a source library, a journal, a contact flow, and a server-side assistant limited to an approved public context, so it answers from reviewed material rather than from whatever a model infers.

| | |
|---|---|
| **Stack** | Plain HTML, CSS, and JavaScript · static build with explicit indexing and cache policy · Cloudflare Pages · Pages Functions · Resend · OpenRouter |
| **Design** | No browser credentials: anything that needs a key runs in a server function. An earlier full-bleed WebGL prototype (React Three Fiber, GSAP, Lenis) remains in the repository; the production page does not depend on it |
| **Status** | Production at [crodax.com](https://crodax.com) |

---

## Autonomous agent systems

### Nous — autonomous business-intelligence and execution platform

A modular platform that runs continuous research, analysis, and operator-supervised execution across a portfolio of ventures. Built as independently tested subsystems rather than a monolith, each with its own test suite and its own contract with the others.

**Architecture**

| Layer | Responsibility |
|---|---|
| `substrate` | Persistence, entity model, provenance and lineage tracking |
| `guardrails` | Policy enforcement, trade-secret filtering, hard blocks on disallowed actions |
| `runtime` / `orchestrator` | Agent lifecycle, scheduling, concurrency, task fan-out |
| `engine` | Domain reasoning and analysis pipelines |
| `discovery` | Source acquisition, deduplication, change detection |
| `learning` | Feedback capture from operator decisions, model adaptation |
| `ledger` / `finance` | Cost accounting, budget ceilings, spend attribution per agent |
| `console` | Operator UI — approvals, queue review, live telemetry |
| `board` / `advisors` | Multi-perspective review before any irreversible action |

**Engineering notes**

- **Human-in-the-loop by construction.** Irreversible or outward-facing actions stop at an approval gate. The operator accepts, rejects, or requests changes; every decision is recorded and becomes training signal.
- **Guardrails are a separate subsystem, not a prompt.** Confidentiality filtering and an approved-facts whitelist run as code between generation and any external surface, so no generated claim escapes unverified.
- **Cost is a first-class constraint.** A ledger meters token spend per agent against explicit budget ceilings, with consumption visible in the console.
- **Local model serving.** A routing layer directs eligible workloads to a self-hosted inference stack on NVIDIA DGX hardware, including a domain-adapted LoRA adapter trained on operator decision history, with a feature flag for instant rollback to hosted models.

| | |
|---|---|
| **Stack** | Python 3.14 · PostgreSQL · 4,200+ pytest tests · TypeScript + React + Vite operator console · vLLM / LoRA fine-tuning on NVIDIA DGX · LaTeX document generation |
| **Status** | Operational — 325 commits |

**NOUS Lavinium** is a separate local-first edition for sales, follow-up, and commercial governance: CRM, evidence-backed fit signals, follow-up planning, scheduling, and quoting from an approved rate card. Its runtime uses SQLite and no third-party Python packages, so it installs as a single macOS application; changing a quote's content invalidates its approval, because an approval only means something for the exact text that was reviewed. It is a first supervised increment: integration adapters exist, and connecting them requires credentials and contracts that are still pending.

---

### OpenOdyssey — local-first agentic desktop environment

A desktop application that treats AI companions as first-class OS citizens with real tool access, running locally by default so that documents, credentials, and conversation history never leave the machine.

**Capabilities**

- **Multi-provider model layer** — one abstraction over 14 providers, including fully local inference via Ollama alongside hosted APIs, switchable per companion.
- **Real tool calling** — companions read and write files, execute shell commands, query the knowledge base, and control the clipboard. Not a chat window with a sandbox around it.
- **Autonomous agent loop** — give a companion a goal; it plans, executes against system tools, observes results, and adapts.
- **Local RAG knowledge base** — document ingestion with locally generated vector embeddings and hybrid semantic + keyword retrieval.
- **OS-grade surfaces** — integrated terminal, file manager, syntax-highlighted editor, IMAP/SMTP mail client, calendar, contacts, live system monitor, clipboard history, media player.

| | |
|---|---|
| **Stack** | Rust (Tauri v2 backend, ~780 KB source) · TypeScript + React frontend (~550 KB) · Ollama for local inference and embeddings · SQLite persistence |
| **Why Tauri** | Native performance and a small binary with direct OS access, without shipping a full browser runtime per app |
| **License** | MIT |

---

### NNC Control — unified command console

A native desktop console for operating a heterogeneous fleet of machines, agents, and long-running jobs from one surface. Deliberately monochromatic and information-dense: a control room, not a dashboard.

- Live agent-orchestration bus with per-worker status
- Graph explorer over a linked knowledge vault, with a purpose-built incremental indexer
- Scheduling and alerting for long-horizon jobs
- Three-state severity model (nominal / warning / critical) applied consistently across every widget, so status is readable at a glance from across a room

| | |
|---|---|
| **Stack** | Rust (Tauri v2, ~400 KB source) · TypeScript + React · custom CSS design system, no component library |
| **Status** | Operational — 32 commits |

---

### Agent Cockpit — terminal observability for multi-agent work

A native terminal console that shows what every coding agent on a machine is doing, across eight CLIs: sessions, subagents, delegation maps, usage and estimated cost per model, and the state of every repository they touch.

It is deliberately an observer, not another orchestrator. It launches nothing and controls nothing; it reads the records those tools already write to disk. That keeps it from becoming a failure point for the work it watches.

**Engineering note.** A session counts as live only on evidence: a recent transcript event or a running console. An agent waiting at a prompt stays visible without its last-event time being rewritten, so the idle age shown is always true.

| | |
|---|---|
| **Stack** | Rust · Ratatui · Crossterm · Notify · Python tooling · 459 Rust tests and 80 Python tests |
| **Status** | Operational — 47 commits |

---

### NNC OS / Nexus — multi-agent cognitive runtime

A long-running research build exploring agent memory, retrieval, and coordination as a persistent runtime rather than a per-request pipeline. It went through several complete architectural revisions — CrewAI-based orchestration, a Svelte + Rust desktop shell, and finally a Python backend with an Electron client — each documented with its own migration and audit record.

| | |
|---|---|
| **Stack** | Python + FastAPI backend · TypeScript / React / Electron client · PostgreSQL · Docker Compose · earlier iterations in Svelte and Rust (Tauri) |
| **Status** | Research lineage, superseded by Nous — retained as architectural reference (82 commits) |

---

### Knowledge-graph substrate — private R&D

A large-scale knowledge-graph system for structured reasoning over ingested corpora, developed across a distributed multi-host cluster with dedicated extraction, verification, and embedding roles.

Architecture, methodology, scale figures, and the associated research findings are **not publicly disclosed**. Some components are subject to pending IP filings and others are held as trade secret.

| | |
|---|---|
| **Stack** (disclosable) | Python · PostgreSQL · vector database · Redis · Alembic migrations · distributed GPU workers |
| **Status** | Private R&D — technical detail available under NDA only |

---

## Applied AI products

### Legal Research Intelligence — AI-assisted legal research and case intelligence

An operational workspace for legal teams: evidence organization, timeline construction, authority tracking, citation validation, and preparation of structured work product for review by qualified professionals.

It is explicitly **not** a chatbot and **not** an autonomous legal decision-maker. That constraint drove the architecture.

**Design principles**

- **Human review is mandatory and enforced.** Every factual finding, citation, translation, and generated report routes through a review workflow with a recorded decision. Nothing is emitted as final without a named reviewer.
- **Provenance is preserved, not asserted.** Document hashes, source registration, and version chains, so any downstream claim traces back to an original artifact.
- **Verified sources are distinguished from unavailable ones.** The system explicitly marks which integrations are live, which are licensed, and which are scaffold-only — the user never has to guess whether a gap is "no results" or "not connected."
- **It boots without paid API keys.** Deterministic local fallbacks for development; those facilities are rejected by production configuration rather than merely discouraged.
- **Fail-closed security.** Malware scanning on ingestion fails closed. External model providers are disabled by default and require explicit configuration.
- **Multilingual intake** — Spanish, English, and Quebec French.

| | |
|---|---|
| **Stack** | Python (~1.1 MB source) · FastAPI · PostgreSQL · TypeScript / React frontend · Docker Compose · ClamAV · pluggable model router supporting local OpenAI-compatible endpoints · MCP adapters |
| **Status** | Controlled local MVP — not approved for production legal matters |

---

### Founders Grip — AI startup evaluation and valuation platform

A commercial platform designed around four one-time paid deliverables: complete business evaluation, company valuation, business plan, and master business plan. The complete business evaluation is the active MVP tier; the other three follow it. The product is a deterministic document pipeline with financial modeling behind it, not a prompt wrapper.

**Design rule.** AI is never authoritative. Scores, formulas, payments, state transitions, permissions, and audit logs stay in deterministic backend code; models classify, narrate, and interpret.

- **Config-driven vertical system** — 19 industry verticals supported through schema-validated configuration rather than code, five active in the first wave
- **Explicit 14-state project machine** with background job execution
- **17-table relational domain model**
- **Valuation engine** with comparable-multiple benchmarking
- Stripe one-time payment integration per tier

| | |
|---|---|
| **Stack** | Python · FastAPI · PostgreSQL + Alembic · Next.js 15 + React 19 frontend · Stripe · Docker Compose · 500+ automated tests on its integration branches |
| **Status** | Core engine complete, hardening in progress |

---

### CRODAX Control Center — funding and strategic-opportunity intelligence

An internal intelligence platform tracking funding programs, accelerators, competitions, and strategic events across Canada/Quebec, the EU, Switzerland, the USA, and global venues.

- **Five-dimension scoring model** (strategic fit, eligibility, financial, timing, leverage) with weighted composition and a strategic-vertical boost
- **Daily discovery pipeline** that deduplicates, rescores, and performs material-change detection against the existing corpus
- **Signal-gated notification** — the team is emailed only when there are genuinely new findings above a fit threshold, or material changes to a tracked program. Silence is the default state, which is what makes the alerts worth reading.
- Deadline surfacing and vertical-coverage analysis through a dark control-room UI

| | |
|---|---|
| **Stack** | Next.js 14 (App Router) · TypeScript · Tailwind CSS · Prisma (SQLite for MVP, PostgreSQL-ready) · Radix UI primitives · Framer Motion · Recharts · Resend · scheduled execution via platform cron and GitHub Actions |
| **Status** | Operational, internal |

---

### Placebook — multiphysics engineering workspace

An engineering workspace where the user describes a calculation in plain language and the system decides which solvers to run and in what order, chains the output of one into the input of the next, and returns a report in which every number traces back to the job that produced it.

The bet is not the solving. Established solvers already do that well, and Placebook bridges to them rather than reimplementing them. The bet is the trail: who asked what, under which assumptions, which number came out of which job, and whether that record survives being handed to an auditor.

**Engineering notes**

- **Capabilities, not solver brands.** The user asks for a result; a planner selects from registered capabilities such as thermophysical properties, thermal and multiphysics FEM, isothermal flash, radial DC power flow, and laminar channel flow. Engineers otherwise pick tools by habit rather than by fit, and the abstraction is what makes swapping a solver a configuration change instead of a migration.
- **No uncited numbers.** A guardrail inspects model output for figures that carry no job reference and forces the citation. A language model that can quietly invent a number is unusable in engineering, so the constraint is enforced in code rather than requested in a prompt.
- **A closed SI unit bus.** When one calculation feeds the next, the travelling unit is validated against an allowlist and the handoff aborts rather than passing a plausible wrong value downstream. Unit mismatches between chained calculations are a classic silent failure, and silence is the part worth engineering away.
- **Explicit degradation.** When an optional dependency is absent, results are returned marked as degraded instead of being quietly approximated.
- **Local by default.** Inference runs against a self-hosted model endpoint, so case material never leaves the operator's own network.

| | |
|---|---|
| **Stack** | Go control plane · Python FastAPI solver workers · Next.js / React console · PostgreSQL · MinIO · NATS JetStream for asynchronous jobs · self-hosted LLM inference · Typst for typeset PDF reports · Docker Compose |
| **Solver bridges** | CoolProp · DOLFINx · OpenFOAM · NeqSim · Kratos · OpenDSS — opt-in, isolated behind a container boundary so a heavy dependency never becomes a hard requirement of the core |
| **Contracts** | 9 JSON Schemas covering job specs, results, trace events, checkpoints, identity, and handoff packages |
| **Status** | In development — 30 commits, seven capabilities registered |

---

## Trade, logistics, and provenance

### Ortressa — multimodal logistics platform and traceability control tower

A compliance-first platform for freight forwarders unifying CRM, multimodal shipment management (ocean, air, road, rail), document intelligence, real-time tracking, predictive ETAs, white-label customer portals, and double-entry accounting — fully self-hosted with no cloud-vendor dependency. On that foundation sits a control tower where a cargo owner sees where goods are, which documents back them, and what state each shipment has reached, without being given access to the wider forwarder back office.

Previously catalogued as CDXCargo. That repository is archived; the complete rebrand reached the Go module, infrastructure identifiers, and browser storage, and development continues here.

This is the most polyglot system in the portfolio, and deliberately so: Go for the transactional core, Python for the ML surface, TypeScript for the operator and customer frontends, with gRPC and Protocol Buffers as the contract between them. Business domains ship as 11 self-registering modules, so a new line of business does not reach into the core.

| Layer | Technology |
|---|---|
| **Backend API** | Go 1.25 · Gin · pgx · Goose migrations · Zerolog · Viper |
| **ML / document service** | Python 3.11 · FastAPI · gRPC · scikit-learn · XGBoost · Optuna · spaCy · PyTesseract · OpenCV · PyMuPDF |
| **Admin frontend** | React 19 · Vite 6 · TanStack Query · TanStack Table · Zustand · React Router 6 |
| **Customer portal** | React 19, white-label with dynamic per-tenant branding |
| **Database** | PostgreSQL 16 + TimescaleDB · 22 migrations |
| **Messaging / cache** | NATS JetStream · Redis 7 |
| **Search** | Meilisearch v1.9 |
| **Object storage** | MinIO (S3-compatible) |
| **Maps / charts** | MapLibre GL JS + OpenStreetMap · Recharts |
| **Edge / observability** | Caddy 2 · Prometheus · Grafana · Loki |
| **Contracts** | Protocol Buffers (proto3) over gRPC |
| **Testing** | 618 Go tests · 124 frontend tests · 34 Python tests |
| **Deployment** | Docker Compose, single-command self-hosted stack |

Document intelligence handles OCR, classification, and structured extraction from bills of lading, invoices, and customs paperwork; predictive ETAs are trained per lane with hyperparameter search rather than served from a single global model.

**Design note.** Document intake models an explicit reviewed state instead of a received/not-received flag. A document that has arrived is not the same as one accepted as evidence, and collapsing the two is what makes a trail indefensible months later when someone asks why a shipment was cleared.

| | |
|---|---|
| **Status** | Working MVP — 40 commits |

---

### CRODAX — cryptographic trust infrastructure

Trust and provenance infrastructure for physical assets moving through supply chains, covering digital trade documentation, asset traceability, and product-passport use cases.

**Patents pending.** Cryptographic scheme, hardware design, and verification protocol are not publicly disclosed.

| | |
|---|---|
| **Disclosable domains** | Electronic bill of lading (eBL) and digital trade documentation · asset traceability · digital product passport (DPP) · ESG and RegTech reporting · post-quantum cryptography readiness |
| **Stack** (disclosable) | TypeScript · Electron desktop verification client · React · Vite · Playwright end-to-end testing |
| **Status** | IP filing in progress — technical detail under NDA and subject to filing status |

---

## Sensing and edge systems

### CSI Platform — commercial WiFi CSI sensing

A commercial-grade platform built around an upstream WiFi channel-state-information (CSI) sensing engine, treated as an untouched dependency. The platform owns everything above the raw signal: topology, zones, calibration, observations, events, incidents, notification and escalation, history, visualization, and operations.

**Development was governed by a written engineering constitution** executed as sequential phase gates — a phase could not begin until the previous phase's acceptance criteria passed:

```
00  TECH STACK ARCHITECTURE        Stack decisions and absolute rules
01  PLATFORM BASELINE              Inventory and backup of existing installation
02  PLATFORM FOUNDATION            Repo scaffold, compose stack, DB, API skeleton
03  PLATFORM CORE                  Registry, observation/event/incident models
04  WEB CONSOLE                    Operational dashboard and floor editor
05  WIFI MAPPING                   Coverage, calibration, drift, fingerprints
06  EVENT ENGINE                   Detector SDK plus detector plugins
06B INCIDENT AND NOTIFICATION      Incident engine, escalation, dispatcher
07  CELLULAR GATEWAY               Gateway SDK plus Android cellular gateway
08  VALIDATION AND OPERATIONS      End-to-end validation, runbooks, monitoring
09  PRODUCTION MVP                 Complete and deployed
```

All phase gates passed. The plugin-based detector SDK means new detection behaviors ship without touching the ingestion path.

| | |
|---|---|
| **Stack** | Python · FastAPI · PostgreSQL + Alembic · TypeScript / React operational console with floor-plan editor · Docker · PowerShell operations tooling · ESP32-C6 sensing nodes streaming CSI over UDP · Android cellular gateway |
| **Status** | MVP complete, every phase gate passed — deployed against a live multi-zone sensor mesh |

---

## Web and SaaS products

### Umbral — report generation SaaS

A commercial SaaS surface covering the full customer lifecycle: authentication with email verification and password reset, Stripe checkout, order management, AI-backed report generation and preview, a private report library with sharing, campaign and newsletter tooling, an admin panel, and a customer dashboard — with SEO handled properly (dynamic sitemap, robots policy, JSON-LD structured data, dynamic OG images) rather than bolted on.

Server-side concerns are implemented as dedicated API route groups (`auth`, `stripe`, `ai`, `campaigns`, `newsletter`, `admin`, `og`) over a Supabase schema managed through versioned SQL migrations and edge functions.

| | |
|---|---|
| **Stack** | Next.js 15 (App Router) · React 19 · TypeScript · Tailwind CSS · Supabase (PostgreSQL, auth, edge functions, SQL migrations) · Stripe checkout |
| **Status** | Feature-complete build |

---

### Founders Grip v2.0 — modular operations platform for small businesses

A separate product from the valuation platform above: guided worksheets, deterministic calculations, sales and inventory, and planning modules for finance, projects, people, cash, marketing, and materials, in English and Spanish.

**Design note.** The system says where each number lives. Inventory has server authority on PostgreSQL when a backend is configured, while the six additional modules stay local to the browser, and the interface does not present a local dashboard as a consolidated ledger. Mixing the two silently is how a planning tool ends up being read as accounting.

| | |
|---|---|
| **Stack** | TypeScript · React 19 · Vite 7 · Supabase (PostgreSQL, edge functions, 15 SQL files) · Rust core crate · Tauri desktop shell · 263 frontend tests |
| **Status** | Local MVP/Beta review candidate — not yet approved for production |

---

## How I work

**Constitution-first on large builds.** Substantial systems (CSI Platform, Nous, Founders Grip) begin with a written engineering constitution: absolute rules, phase decomposition, and explicit acceptance criteria per phase. A phase does not start until the previous one passes. This is why those builds have audit trails instead of archaeology.

**Self-hosted and vendor-independent by default.** Ortressa, Legal Research Intelligence, and CSI Platform all run as single-command Docker Compose stacks with no managed-cloud dependency. Portability is a requirement, not an aspiration — several of these systems operate in jurisdictions or industries where data residency is not negotiable.

**Guardrails as code, not as instructions.** In systems where an agent can act outward, confidentiality filtering and approval gates are separate enforced subsystems sitting between generation and any external surface. Policy that lives only in a prompt is not policy.

**Tests scale with irreversibility.** Founders Grip carries 500+ automated tests on its integration branches; Nous runs 4,200+ tests, with real-database suites using transactional rollback fixtures. Systems that only render get visual and end-to-end coverage instead. I do not apply a uniform coverage number to non-uniform risk.

**Polyglot where it earns its keep.** Go for transactional throughput, Python for ML and data pipelines, Rust for native desktop shells, TypeScript everywhere a human looks. Boundaries are explicit contracts — gRPC with Protocol Buffers, typed HTTP APIs — never shared mutable state across a language boundary.

**Documented handoffs.** Every long-running project carries handoff documents, architecture maps, and execution logs written for whoever picks it up next, including future me.

---

## Technology

| Domain | Technologies |
|---|---|
| **Languages** | Python · TypeScript · Go · Rust · SQL · Bash · PowerShell |
| **Backend** | FastAPI · Gin · Next.js API routes · gRPC / Protocol Buffers · NATS JetStream · Celery-style background workers |
| **Frontend** | React 19 · Next.js 14/15 · Astro · Vite · Tailwind CSS · Radix UI · TanStack Query / Table · Zustand · Framer Motion · GSAP · Three.js / React Three Fiber · Recharts · MapLibre GL |
| **Desktop / terminal** | Tauri v2 (Rust) · Electron · Ratatui |
| **Data** | PostgreSQL 16 · TimescaleDB · SQLite · Supabase · Redis · Meilisearch · vector databases · Prisma · Alembic · Goose |
| **AI / ML** | Multi-provider LLM routing (Anthropic, OpenAI, Gemini, Groq, DeepSeek, Mistral, OpenRouter, Ollama) · vLLM self-hosted serving · LoRA fine-tuning on NVIDIA DGX · RAG and hybrid retrieval · knowledge graphs · MCP (Model Context Protocol) · scikit-learn · XGBoost · Optuna · spaCy · OpenCV · PyTesseract |
| **Infrastructure** | Docker & Docker Compose · Caddy · MinIO · Prometheus · Grafana · Loki · GitHub Actions · Cloudflare Pages and Functions · Tailscale |
| **Testing** | pytest · Go testing · Rust tests · Vitest · Playwright · visual regression · end-to-end contract testing |
| **Edge / IoT** | Raspberry Pi · Arduino · ESP32 / ESP32-C6 · WiFi CSI sensing · high-frequency DAQ and sensor logging · UDP telemetry ingestion · Android gateway |

---

## Requesting access

The repositories described above are private. I grant **time-boxed read access** to individuals and teams with a concrete reason — hiring evaluation, technical due diligence, partnership assessment, or code review.

**To request access, email [tomas.houdely@gmail.com](mailto:tomas.houdely@gmail.com) with:**

1. Your GitHub username
2. Which project or projects you want to see
3. Who you are and why — one or two sentences is enough

**What you get:** read access to the relevant repositories for an agreed window, and a walkthrough call if that would be more useful than reading alone.

**What I cannot share:** source or technical detail for anything covered by pending IP filings or held as trade secret. For those, an NDA-covered technical discussion is available instead — the constraint is on written disclosure, not on conversation.

Requests from recruiters, engineers, investors, and potential collaborators are all welcome. I answer every one.

---

<sub>Catalog maintained manually. Descriptions reflect architecture and scope only — no credentials, endpoints, protected schemes, or client data appear in this repository.</sub>
