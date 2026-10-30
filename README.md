<div align="center">

# Multi-Agent RAG Debugger

### Evidence-Driven Diagnostics for Retrieval-Augmented Generation Systems

**Debug RAG pipelines. Investigate retrieval failures. Generate grounded answers. Diagnose root causes. Repair code in an isolated candidate workflow.**

[![Python](https://img.shields.io/badge/Python-3.13%2B-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![FastAPI](https://img.shields.io/badge/FastAPI-Backend-009688?logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com/)
[![React](https://img.shields.io/badge/React-Frontend-61DAFB?logo=react&logoColor=black)](https://react.dev/)
[![FAISS](https://img.shields.io/badge/Vector_Search-FAISS-0467DF)](https://github.com/facebookresearch/faiss)
[![License](https://img.shields.io/badge/License-TBD-lightgrey.svg)](#license)

</div>

---

## Overview

**Multi-Agent RAG Debugger** is a modular AI engineering platform for investigating, evaluating, and improving Retrieval-Augmented Generation (RAG) pipelines.

It combines document ingestion, hybrid retrieval, grounded answer generation, multi-agent diagnostics, evaluation metrics, and a repository repair workflow in a unified application.

The primary workspace, **Atlas Repair**, allows users to report an error, connect a repository accessible to the API, review an evidence-backed diagnosis and proposed patch, approve application to an isolated candidate, and run tests.

The document RAG debugger remains available in the UI, alongside a legacy code-debugging workflow.

The platform is designed to distinguish **measured system behavior from model-generated judgments**. Retrieval scores, evaluation metrics, token usage, and other measurements should come from actual execution or explicitly documented estimates—not fabricated dashboard values.

### Core Capabilities

- **Document ingestion:** Upload TXT, Markdown, PDF, DOCX, and JSON documents.
- **Persistent indexing:** Extract, chunk, embed, and persist documents with chunk provenance.
- **Hybrid retrieval:** Combine lexical and semantic search using BM25 and FAISS.
- **Grounded generation:** Generate answers using retrieved context, with optional citations.
- **Multi-agent diagnostics:** Investigate retrieval, chunking, embeddings, generation, faithfulness, and root causes.
- **RAG evaluation:** Calculate retrieval and ranking metrics when appropriate ground-truth labels are available.
- **Experiment tracking:** Persist debug reports, evaluation datasets, and experiment metrics.
- **Code repair workflow:** Investigate repository issues, propose patches, review changes, and execute tests through the candidate workflow.
- **Flexible model providers:** Support configured OpenAI, OpenRouter, Ollama, and mock LLM adapters.
- **Offline development:** Run with explicit mock LLM and hash embedding providers.

---

## Product Workflows

### 1. Atlas Repair

The primary workspace provides an issue-to-patch workflow:

1. Report an error or describe a repository issue.
2. Connect a repository the API can access.
3. Investigate the available evidence.
4. Review the diagnosis and proposed patch.
5. Approve applying the patch to an isolated candidate.
6. Run tests against the candidate and inspect the results.

The workflow separates diagnosis, patch review, candidate application, and testing rather than treating a model-generated patch as automatically verified.

**Important:** A proposed fix is not proof of correctness. Validation depends on the tests and checks actually executed.

### 2. RAG Debugger

Use the document workspace to investigate a RAG pipeline:

1. Create a workspace-scoped knowledge base.
2. Upload documents.
3. Inspect document ingestion, chunking, and indexing.
4. Query the knowledge base using lexical, semantic, or hybrid retrieval.
5. Generate an answer from retrieved context, or skip generation.
6. Run diagnostic analyses.
7. Review evidence, evaluation results, and persisted reports.

### 3. Code Lab

The legacy code-debugging lab remains available through the frontend **Code Lab** tab and the `/api/v1/debugging-runs` API.

It provides an issue-to-patch-to-sandbox workflow separate from the primary Atlas Repair workspace.

---

## Architecture

```text
                          ┌─────────────────────────┐
                          │     React Frontend      │
                          │ Atlas Repair / RAG UI   │
                          │       / Code Lab        │
                          └────────────┬────────────┘
                                       │
                                       ▼
                          ┌─────────────────────────┐
                          │      FastAPI API        │
                          │ Auth / Workspace Scope  │
                          │ Request Validation      │
                          └────────────┬────────────┘
                                       │
                  ┌────────────────────┼────────────────────┐
                  │                    │                    │
                  ▼                    ▼                    ▼
        ┌──────────────────┐ ┌──────────────────┐ ┌──────────────────┐
        │ Knowledge Base   │ │ RAG Diagnostics   │ │ Code Repair      │
        │ and Ingestion    │ │ and Evaluation    │ │ and Debugging    │
        └────────┬─────────┘ └────────┬─────────┘ └──────────────────┘
                 │                    │
                 ▼                    ▼
        ┌──────────────────┐ ┌────────────────────────────┐
        │ Extract / Chunk  │ │ Diagnostic Analysts        │
        │ Embed / Persist  │ │ Retrieval / Chunking       │
        └────────┬─────────┘ │ Embedding / Generation     │
                 │           │ Faithfulness / Root Cause  │
                 ▼           └──────────────┬─────────────┘
        ┌──────────────────┐                │
        │ FAISS + BM25     │                │
        │ Hybrid Retrieval │                │
        └────────┬─────────┘                │
                 │                          │
                 └────────────┬─────────────┘
                              ▼
                  ┌────────────────────────┐
                  │ Reports and Evaluation │
                  │ Persistent Metadata    │
                  │ Experiment Metrics     │
                  └────────────────────────┘
```

### Application Layers

| Package | Responsibility |
|---|---|
| `app/domain` | Domain schemas, data models, and enums |
| `app/rag` | Ingestion, chunking, embeddings, indexing, retrieval, and metrics |
| `app/agents` | Diagnostic analysts and orchestration |
| `app/application` | Knowledge base, debugging, and evaluation services |
| `app/api` | FastAPI routes, authentication dependencies, and error handling |
| `app/db` | SQLAlchemy models and database sessions |
| `app/llm` | OpenAI, OpenRouter, Ollama, and mock adapters |
| `frontend` | React application and product workspaces |
| `tests` | API, evaluation, and security tests |
| `docs` | Architecture assessments and debugging documentation |

For additional implementation details, see:

- `docs/REPOSITORY_ASSESSMENT.md`
- `docs/RAG_DEBUGGER.md`
- `data/README.md`

---

## RAG Pipeline

The core RAG workflow follows this sequence:

```text
Source Documents
      │
      ▼
Document Extraction
      │
      ▼
Chunking + Provenance
      │
      ▼
Embedding Generation
      │
      ▼
FAISS Vector Index + BM25 Lexical Retrieval
      │
      ▼
Hybrid Retrieval + Optional Reranking
      │
      ▼
Retrieved Context
      │
      ├──────────────► Retrieval Diagnostics
      │
      ▼
Optional LLM Generation
      │
      ▼
Grounded Answer + Citations
      │
      ▼
Faithfulness Analysis + Root-Cause Investigation
      │
      ▼
Evaluation Results + Persisted Reports
```

### Retrieval Modes

| Mode | Description |
|---|---|
| Lexical | Retrieves documents using term-based matching |
| Semantic | Uses embeddings and vector similarity |
| Hybrid | Combines lexical and semantic retrieval |
| Reranked | Optionally reranks retrieved candidates |

Available modes and provider behavior depend on the configured application settings.

### Multi-Agent Diagnostics

The diagnostic system includes analysts for:

- **Retrieval:** Investigates candidate selection, ranking behavior, and retrieval evidence.
- **Chunking:** Examines chunk boundaries, sizes, and provenance.
- **Embeddings:** Investigates embedding configuration and available embedding evidence.
- **Generation:** Reviews answer generation and the retrieved context supplied to the model.
- **Faithfulness:** Evaluates whether generated claims are supported by the available evidence.
- **Root cause:** Synthesizes diagnostic findings into a proposed explanation of pipeline failures.

These analysts support investigation; their conclusions are not automatically equivalent to experimentally verified root causes.

---

## Evaluation and Observability

The platform supports retrieval evaluation, diagnostic reports, datasets, and experiment metrics.

When relevant document IDs are labeled, the system can calculate metrics such as:

| Metric | Purpose |
|---|---|
| Precision@k | Measures the fraction of retrieved top-k documents that are relevant |
| Recall@k | Measures the fraction of labeled relevant documents retrieved in the top k |
| MRR | Measures the reciprocal rank of the first relevant result |
| nDCG@k | Evaluates ranking quality while accounting for relevance and rank position |

**Ground-truth requirement:** These metrics are meaningful only when relevant-document labels are available and the evaluation protocol is defined. The application must not present unlabeled retrieval results as measured precision, recall, MRR, or nDCG.

### Measurement Integrity

The platform follows several important principles:

- Retrieval scores should originate from actual retrieval execution.
- Evaluation metrics require the appropriate relevance labels.
- Token usage should come from provider-reported usage or a clearly identified estimate.
- Model-generated diagnostic judgments should be distinguishable from deterministic measurements.
- Failed external providers should not silently appear as successful real-model executions.
- Mock LLM and hash embedding modes are explicit development configurations.

These distinctions help prevent misleading evaluations and false confidence in debugging results.

---

## Technology Stack

| Component | Technology |
|---|---|
| Backend API | Python, FastAPI |
| Frontend | React, Node.js, Vite-based development workflow |
| Vector search | FAISS |
| Lexical retrieval | BM25 |
| ORM | SQLAlchemy |
| Database migrations | Alembic |
| Database | SQLite for the default local workflow; PostgreSQL for configured deployments |
| LLM integration | OpenAI, OpenRouter, Ollama, mock adapter |
| Embeddings | Hash embeddings, local sentence-transformer embeddings, OpenAI embeddings |
| Testing | pytest |
| Deployment | Docker Compose |

The technologies listed above reflect the intended application architecture. Actual provider availability and runtime behavior depend on the installed dependencies and environment configuration.

---

## Quickstart

### Prerequisites

- Python 3.13+
- Node.js 20+
- npm
- Git

Docker is optional if you prefer the local development workflow.

### 1. Clone the Repository

```bash
git clone https://github.com/YOUR_USERNAME/multi-agent-rag-debugger.git

cd multi-agent-rag-debugger
```

Replace `YOUR_USERNAME` with your GitHub username or organization.

### 2. Create a Python Virtual Environment

**Windows PowerShell:**

```powershell
python -m venv .venv

.\.venv\Scripts\Activate.ps1

python -m pip install --upgrade pip

pip install -r requirements.txt
```

**Linux / macOS:**

```bash
python3 -m venv .venv

source .venv/bin/activate

python -m pip install --upgrade pip

pip install -r requirements.txt
```

### 3. Configure Environment Variables

**Windows PowerShell:**

```powershell
Copy-Item .env.example .env
```

**Linux / macOS:**

```bash
cp .env.example .env
```

Review `.env` before starting the application.

The example configuration uses:

```dotenv
LLM_PROVIDER=mock
EMBEDDING_PROVIDER=hash
```

These modes are intended to make local development possible without requiring external model credentials.

Do not commit `.env` or API keys to Git.

### 4. Start the Backend

From the repository root:

```bash
python -m uvicorn app.api.main:app --host 127.0.0.1 --port 8000 --reload
```

The API will listen on:

```text
http://127.0.0.1:8000
```

### 5. Start the Frontend

Open another terminal:

```bash
cd frontend

npm install

npm run dev
```

Open the application in your browser:

| Service | URL |
|---|---|
| Frontend | `http://localhost:5173` |
| OpenAPI documentation | `http://127.0.0.1:8000/docs` |
| Health endpoint | `http://127.0.0.1:8000/api/v1/health` |

The frontend development server is expected to proxy API requests to `127.0.0.1:8000`.

**Troubleshooting:** If the frontend displays `Failed to fetch` or `API offline`, verify that the backend is running on IPv4 port 8000. On Windows, binding explicitly to `127.0.0.1` can avoid hostname resolution issues. Leave `VITE_API_URL` empty when relying on the configured Vite proxy.

---

## Model and Embedding Configuration

### LLM Providers

The application includes adapters for the following providers:

| Provider | Configuration | Requirements |
|---|---|---|
| Mock | `LLM_PROVIDER=mock` | No external credentials |
| OpenAI | `LLM_PROVIDER=openai` | Valid OpenAI API key and supported model configuration |
| OpenRouter | `LLM_PROVIDER=openrouter` | Valid OpenRouter credentials and supported model configuration |
| Ollama | `LLM_PROVIDER=ollama` | Reachable Ollama service and an available model |

Use the environment variable names and additional settings defined in `.env.example`.

### Embedding Providers

| Provider | Configuration | Notes |
|---|---|---|
| Hash | `EMBEDDING_PROVIDER=hash` | Useful for offline development; not semantic embeddings |
| Local | `EMBEDDING_PROVIDER=local` | Requires compatible local embedding dependencies and model weights |
| OpenAI | `EMBEDDING_PROVIDER=openai` | Requires valid credentials and supported embedding configuration |

**Important:** Hash embeddings are not a substitute for semantic embedding models. They can support development and deterministic testing, but they should not be used to claim production-quality semantic retrieval.

Switching embedding models for an existing index may require re-embedding and rebuilding the index.

---

## Database Migrations

The default SQLite workflow initializes database tables through `create_all` during API startup.

For deployments that use Alembic migrations, apply the available migrations with:

```bash
python -m alembic upgrade head
```

For a fresh PostgreSQL volume, use the migration workflow when required by your deployment configuration.

Review the migration configuration and database environment variables before applying migrations to a shared or production database.

---

## Running Tests

Run the test suite from the repository root:

```bash
python -m pytest tests -q
```

Relevant test modules include:

```text
tests/
├── test_rag_product_api.py
├── test_rag_metrics.py
└── test_security_controls.py
```

These tests use the configured mock LLM and hash embeddings where applicable.

**Test scope matters:** Passing offline tests does not establish that an external OpenAI or OpenRouter endpoint is reachable, that local model weights are available, or that a production deployment is correctly configured.

For repository repair workflows, inspect the available fixtures and instructions in `data/README.md`. Some demo fixtures are intentionally expected to fail until repaired.

---

## Docker Deployment

If Docker Compose is configured for your environment, start the application with:

```bash
docker compose up --build
```

For detached execution:

```bash
docker compose up --build -d
```

Review the Compose configuration and environment variables before exposing services beyond your local machine.

By default, the development-oriented configuration uses mock LLM and hash embeddings unless provider settings are supplied.

The configured PostgreSQL service stores application metadata, while vector search uses persisted FAISS indexes mounted under `./data`.

**Production deployment requires additional review** of authentication, secret management, network exposure, storage permissions, and candidate execution isolation.

---

## Security Considerations

Security controls are essential because the platform processes user documents and may inspect or modify source code.

### Authentication

- Set `API_AUTH_TOKEN` for shared environments.
- An empty API token configuration may leave the API open for local development.
- Configure `AUTH_MODE=required` where required by the deployment.
- Register and configure users through the supported Settings workflow.

### Workspace Isolation

- Workspace-scoped operations use `X-Workspace-Id` at the service layer.
- Combine workspace isolation with authentication in shared deployments.
- Validate authorization on every resource access; a workspace header alone is not an authentication mechanism.

### Document Processing

- Uploaded documents are subject to configured file-type and size limits.
- Treat document content as untrusted input.
- Retrieved content must not be allowed to override system instructions or authorize privileged operations.

### Code Repair and Candidate Execution

- Review proposed changes before applying them.
- Keep candidate modifications isolated from the primary working tree.
- Avoid executing untrusted repository code with unrestricted host permissions.
- Use least-privilege credentials and restrict network and filesystem access for untrusted code.
- Do not treat a generated patch as safe merely because an LLM recommends it.

### Secrets and Data

Never commit:

```text
.env
API keys
Provider credentials
data/*.db
Private repository credentials
Sensitive uploaded documents
```

Use appropriate secret management, access controls, and retention policies for shared deployments.

---

## Known Limitations

The following constraints should be considered when evaluating the application:

1. **Inline ingestion:** Document ingestion runs inside the API process when `JOB_EXECUTION_MODE=inline`. Large uploads can exceed request time limits; smaller batches or background processing may be necessary.
2. **Hash embeddings:** Hash embeddings are intended for offline development and do not provide semantic embedding quality.
3. **Faithfulness evaluation:** Lexical overlap and LLM-based judging provide evaluation signals, not guarantees of factual correctness.
4. **Runtime settings:** The runtime `POST /api/v1/settings` endpoint mutates process memory only; it is not a durable settings store.
5. **Workspace identity:** Workspace-header-based isolation is not a complete multi-user identity and authorization system.
6. **External provider dependencies:** Real-model execution requires correctly configured credentials, endpoints, dependencies, and model availability.
7. **Evaluation labels:** Ranking metrics require labeled relevance data and a defined evaluation protocol.
8. **Candidate verification:** A proposed code patch is not verified until the relevant tests and checks have actually executed and their results have been inspected.

---

## Project Documentation

Additional documentation is available in the repository:

| Document | Purpose |
|---|---|
| `docs/REPOSITORY_ASSESSMENT.md` | Repository architecture and assessment |
| `docs/RAG_DEBUGGER.md` | RAG debugging workflow and implementation details |
| `data/README.md` | Demo data and fixture instructions |

---

## Roadmap

Potential future improvements include:

- Background ingestion jobs and asynchronous processing.
- More comprehensive retrieval evaluation datasets.
- Persistent experiment comparisons and regression detection.
- Expanded retrieval and generation observability.
- Stronger multi-user authentication and authorization.
- Sandboxed candidate execution with resource limits.
- Automated regression testing for RAG configuration changes.
- Provider integration tests and deployment health checks.

This roadmap describes potential improvements, not guarantees that these features are already implemented.

---

## Contributing

Contributions, bug reports, and architecture discussions are welcome.

A typical contribution workflow:

1. Fork the repository.
2. Create a feature branch.
3. Implement and test your changes.
4. Run the relevant test suite.
5. Open a pull request describing the change, its motivation, and its validation results.

For substantial changes, include tests and document any new environment variables or provider requirements.
