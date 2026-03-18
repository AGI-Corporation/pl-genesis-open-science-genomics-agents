# PL-Genesis Open Science Genomics Agents

> **PL Genesis: Frontiers of Collaboration Hackathon** | Track: AI & Robotics · Crypto (DeSci) · NEAR · Storacha · Infrastructure & Digital Rights

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![AGI Corporation](https://img.shields.io/badge/org-AGI--Corporation-blue)](https://github.com/AGI-Corporation)
[![Hackathon](https://img.shields.io/badge/hackathon-PL%20Genesis-purple)](https://pl-genesis-frontiers-of-collaboration-hackathon.devspot.app)

## Overview

A **multi-agent research coordinator for genomics and DeSci studies**. It runs end-to-end workflows from cohort selection to analysis plan drafting and scientific reporting. Each agent handles a specialized task: literature review, data QC, protocol drafting, or experiment tracking.

**Core repos integrated:**
- [`genomic-go-platform`](https://github.com/AGI-Corporation/genomic-go-platform) — cohort, sample, assay, and analysis pipeline schemas
- [`Sapient.x`](https://github.com/AGI-Corporation/Sapient.x) — precomputed omics analysis templates & biomarker playbooks
- [`Route.X`](https://github.com/AGI-Corporation/Route.X) — study pipeline orchestration
- [`CMMC`](https://github.com/AGI-Corporation/CMMC) — data access governance and de-identification policies
- [`AGI-Framework`](https://github.com/AGI-Corporation/AGI-Framework) — conversational genomics agent (eliza-based)
- [`deep-research`](https://github.com/AGI-Corporation/deep-research) — Letta-style research pipelines
- [`swarms`](https://github.com/AGI-Corporation/swarms) — multi-agent orchestration

---

## Goals

- Coordinate multi-agent workflows for genomics studies (cohort design → analysis plan → reporting)
- Use genomic-go and Sapient.x as the domain data layer
- Demonstrate AI agents reducing study setup time from weeks to hours
- Integrate NEAR bounties for community contributor rewards
- Store all task artifacts on Storacha (decentralized, persistent)

---

## Hackathon Tracks & Sponsors

| Sponsor / Track | How this project qualifies |
|---|---|
| **AI & Robotics** | Agents doing real science — lit review, QC, protocol design |
| **Crypto** — DeSci & Public Goods | Bounties and retroactive rewards for study contributions |
| **NEAR** — AI that works for you | NEAR smart contracts manage bounties, attestation, and rewards |
| **Storacha** | Decentralized task queues, artifact registry, shared agent state |
| **Infrastructure & Digital Rights** | CMMC data governance across agent tasks |
| **Fresh/Existing Code** | Reuses genomic-go, Sapient.x, Route.X, deep-research, swarms |

---

## Architecture

```
┌─────────────────────────────────────────────────────────┐
│           Chat UI / Research Console (React)            │
└──────────────────────┬──────────────────────────────────┘
                       │ REST / WebSocket
┌──────────────────────▼──────────────────────────────────┐
│              Orchestrator API (FastAPI)                  │
│                                                         │
│  ┌─────────────────────────────────────────────────┐   │
│  │           Route.X Study Pipeline                │   │
│  │  define_question → suggest_datasets →           │   │
│  │  design_cohort → draft_protocol → run → report  │   │
│  └────────────────┬────────────────────────────────┘   │
│                   │ spawns tasks                        │
│  ┌────────────────▼────────────────────────────────┐   │
│  │         Swarms Agent Pool                        │   │
│  │  LitReviewAgent │ DataQCAgent │ ProtocolAgent    │   │
│  │  AnalysisAgent  │ ReportAgent │ BountyAgent      │   │
│  └──────┬──────────────────────────────────────────┘   │
│         │                                               │
│  ┌──────▼──────┐  ┌──────────────┐  ┌──────────────┐  │
│  │ genomic-go  │  │  Sapient.x   │  │  CMMC        │  │
│  │ Cohorts/    │  │  Templates   │  │  Policies    │  │
│  │ Datasets    │  │  Playbooks   │  │  Access ctrl │  │
│  └─────────────┘  └──────────────┘  └──────────────┘  │
└──────────────────────┬──────────────────────────────────┘
                       │
       ┌───────────────┴───────────────┐
       ▼                               ▼
  Storacha                        NEAR Contracts
  (artifacts,                     (bounties,
   task logs,                      attestations,
   RAG knowledge base)             rewards)
```

---

## Data Schema

```typescript
Study {
  id: string
  title: string
  PI_id: string
  domain: "genomics" | "proteomics" | "multi-omics" | "clinical"
  research_question: string
  status: "draft" | "active" | "complete"
  linked_grant_program_id?: string
}

Cohort {
  id: string
  genomic_go_cohort_ref: string
  inclusion_criteria: string[]
  exclusion_criteria: string[]
  sample_size: number
  modalities: string[]  // ["WGS", "RNA-seq", "proteomics"]
}

AgentTask {
  id: string
  study_id: string
  task_type: "lit_review" | "data_cleaning" | "cohort_design" | "analysis_plan" | "report"
  assigned_agent: string
  input_artifacts: string[]
  output_artifacts: string[]
  status: "queued" | "running" | "complete" | "failed"
  storacha_log_uri: string
}

Artifact {
  id: string
  study_id: string
  type: "pdf" | "notebook" | "plot" | "markdown" | "model"
  uri: string  // Storacha CID
  checksum: string
  created_by_agent_id: string
  timestamp: string
}

Bounty {
  id: string
  study_id: string
  task_type: string
  reward_near: number
  claimant_id?: string
  near_contract_address: string
  status: "open" | "claimed" | "paid"
}
```

---

## Repo Structure

```
pl-genesis-open-science-genomics-agents/
├── README.md
├── docs/
│   ├── architecture.md
│   ├── agent-specs.md
│   └── demo-script.md
├── orchestrator/              # FastAPI Orchestrator
│   ├── app/
│   │   ├── routes/
│   │   │   ├── studies.py
│   │   │   ├── tasks.py
│   │   │   └── artifacts.py
│   │   ├── services/
│   │   │   ├── routex_client.py
│   │   │   ├── genomicgo_client.py
│   │   │   ├── sapientx_client.py
│   │   │   ├── cmmc_client.py
│   │   │   ├── storacha_client.py
│   │   │   └── near_client.py
│   │   └── models/
│   ├── requirements.txt
│   └── Dockerfile
├── agents/                    # Swarms-based agent implementations
│   ├── lit_review_agent.py
│   ├── data_qc_agent.py
│   ├── cohort_design_agent.py
│   ├── analysis_plan_agent.py
│   └── report_agent.py
├── routex/
│   └── workflows/
│       └── genomics-study-pipeline.yml
├── frontend/                  # React Chat UI
│   ├── src/
│   │   ├── pages/
│   │   │   ├── StudyConsole.tsx
│   │   │   ├── ArtifactViewer.tsx
│   │   │   └── BountyBoard.tsx
│   └── package.json
├── near/
│   └── contracts/
│       └── bounty.rs
└── docker-compose.yml
```

---

## API Endpoints (MVP)

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/studies` | Create study from natural language description |
| `GET` | `/studies/{id}` | Status + suggested cohorts + analysis plan |
| `POST` | `/studies/{id}/run-step` | Execute a Route.X step (e.g. `suggest_datasets`) |
| `GET` | `/studies/{id}/artifacts` | List all produced artifacts (markdown, PDFs, notebooks) |
| `GET` | `/studies/{id}/tasks` | List all agent tasks and their statuses |
| `POST` | `/bounties` | Post a bounty for a study task |
| `POST` | `/bounties/{id}/claim` | Claim a bounty (triggers NEAR contract) |

---

## User Stories

1. **Principal Investigator** — Describe a research question in natural language, get a draft study design with cohort and analysis plan
2. **Data Steward** — See exactly which genomic-go datasets are requested and under what CMMC policies
3. **Community Collaborator** — Claim study tasks as NEAR bounties and get paid for contributions
4. **Lab Admin** — Monitor all running agent tasks, review artifacts, manage study status

---

## Demo Script

1. Input a genomics research question: *"Find biomarkers predictive of therapy response in NSCLC cohorts"*
2. Route.X workflow view shows pipeline steps (JSON/visual)
3. Run **"Suggest datasets"**: UI shows 2-3 genomic-go datasets with justifications
4. Run **"Draft cohort + analysis plan"**: display crisp markdown plan referencing Sapient.x templates
5. Show Storacha artifact CID for each produced output
6. Post a bounty on NEAR for the report-writing task

---

## Tech Stack

| Layer | Technology |
|-------|------------|
| Agent Runtime | swarms + deep-research (Letta-style pipelines) |
| LLM Frontend | AGI-Framework (eliza) |
| Orchestration | Route.X |
| Domain Models | genomic-go-platform + Sapient.x |
| Storage | Storacha (task logs, artifacts, RAG KB) |
| On-chain Bounties | NEAR smart contracts (Rust) |
| Policy Engine | CMMC |
| Backend | FastAPI (Python) |
| Frontend | React + TailwindCSS |

---

## Getting Started

```bash
git clone https://github.com/AGI-Corporation/pl-genesis-open-science-genomics-agents
cd pl-genesis-open-science-genomics-agents
cp .env.example .env
docker-compose up
```

Visit `http://localhost:3000` for the chat UI and `http://localhost:8000/docs` for the API.

---

## License

MIT — see [LICENSE](LICENSE)

---

*Built for the [PL Genesis: Frontiers of Collaboration Hackathon](https://pl-genesis-frontiers-of-collaboration-hackathon.devspot.app) by AGI Corporation*
