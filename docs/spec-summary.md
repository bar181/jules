/docs/spec-summary.md

# Project Details: Jules-Inspired AI Coding Assistant

**Document Version:** 1.0
**Date:** `YYYY-MM-DD`
**Project Repository:** `bar191/jules`
**Primary Development Environment:** GitHub Codespaces (configured for Python/Node.js/Docker)
**Target Audience:** AI Code Editor/Writer, Development Team, Project Stakeholders

## 0. Table of Contents

1.  [Introduction & Vision](#1-introduction--vision)
    *   [1.1. Project Goal](#11-project-goal)
    *   [1.2. Inspiration (Google's Jules)](#12-inspiration-googles-jules)
    *   [1.3. Core Value Proposition](#13-core-value-proposition)
    *   [1.4. Target Users](#14-target-users)
2.  [Preamble for AI Code Editor/Writer](#2-preamble-for-ai-code-editorwriter)
    *   [2.1. Core Principles for AI Implementation](#21-core-principles-for-ai-implementation)
    *   [2.2. Key Technologies](#22-key-technologies)
    *   [2.3. Development Environment (GitHub Codespaces)](#23-development-environment-github-codespaces)
3.  [System Architecture](#3-system-architecture)
    *   [3.1. High-Level Architecture Diagram (Mermaid)](#31-high-level-architecture-diagram-mermaid)
    *   [3.2. Component Descriptions](#32-component-descriptions)
    *   [3.3. Technology Stack Summary](#33-technology-stack-summary)
4.  [Core Process Flow Diagram (Mermaid)](#4-core-process-flow-diagram-mermaid)
5.  [Functional Specifications (MVP & Beyond)](#5-functional-specifications-mvp--beyond)
    *   [5.1. MVP Core Features](#51-mvp-core-features)
    *   [5.2. Key Differentiators (Inspired by Jules)](#52-key-differentiators-inspired-by-jules)
6.  [Technical Specifications](#6-technical-specifications)
    *   [6.1. Frontend (React/Vite/Tailwind)](#61-frontend-reactvitetailwind)
    *   [6.2. Backend (FastAPI, Python)](#62-backend-fastapi-python)
    *   [6.3. Task Execution Environment (Containerized Workers)](#63-task-execution-environment-containerized-workers)
    *   [6.4. Database (Supabase/Postgres)](#64-database-supabasepostgres)
    *   [6.5. Authentication (Supabase Auth with GitHub OAuth)](#65-authentication-supabase-auth-with-github-oauth)
    *   [6.6. GitHub Integration (API & GitHub App)](#66-github-integration-api--github-app)
    *   [6.7. AI Orchestration (LiteLLM & LLMs)](#67-ai-orchestration-litellm--llms)
    *   [6.8. Audio Summary Service (TTS)](#68-audio-summary-service-tts)
    *   [6.9. Real-Time Communication (WebSockets)](#69-real-time-communication-websockets)
7.  [Data Specifications](#7-data-specifications)
    *   [7.1. Database Schema (Supabase/Postgres)](#71-database-schema-supabasepostgres)
    *   [7.2. API Payload Examples](#72-api-payload-examples)
    *   [7.3. WebSocket Message Formats](#73-websocket-message-formats)
    *   [7.4. Environment Variables](#74-environment-variables)
8.  [Implementation Plan: Phased Approach](#8-implementation-plan-phased-approach)
    *   [8.1. MVP Development Phases (Revised 6-Phase Plan)](#81-mvp-development-phases-revised-6-phase-plan)
        *   [Phase 1: Setup & Authentication UI Stub](#phase-1-setup--authentication-ui-stub)
        *   [Phase 2: Containerized Worker & Basic Task Queue](#phase-2-containerized-worker--basic-task-queue)
        *   [Phase 3: Core Workflow (Clone, Plan, Diff, Logs) in Container](#phase-3-core-workflow-clone-plan-diff-logs-in-container)
        *   [Phase 4: Tests, Commit, PR Creation & Audio Summary](#phase-4-tests-commit-pr-creation--audio-summary)
        *   [Phase 5: GitHub App Webhook Trigger & Basic Quotas](#phase-5-github-app-webhook-trigger--basic-quotas)
        *   [Phase 6: LLM Selector, UX Polish, Docs & MVP Launch](#phase-6-llm-selector-ux-polish-docs--mvp-launch)
    *   [8.2. Detailed Phase Plans Location](#82-detailed-phase-plans-location)
9.  [Post-MVP Roadmap (Future Enhancements)](#9-post-mvp-roadmap-future-enhancements)
    *   [Tier 1: Core Robustness & Intelligence](#tier-1-core-robustness--intelligence)
    *   [Tier 2: User Experience & Operational Polish](#tier-2-user-experience--operational-polish)
    *   [Tier 3: Advanced Capabilities & Ecosystem Integration](#tier-3-advanced-capabilities--ecosystem-integration)
10. [Security Considerations](#10-security-considerations)
11. [Testing Strategy](#11-testing-strategy)
12. [Deployment Strategy (Post-MVP)](#12-deployment-strategy-post-mvp)
13. [Contribution Guidelines (for AI and Human Developers)](#13-contribution-guidelines-for-ai-and-human-developers)
14. [Glossary](#14-glossary)
15. [Document Version History](#15-document-version-history)

---

## 1. Introduction & Vision

### 1.1. Project Goal
To develop "Jules-MVP," an open-source AI coding assistant inspired by Google's Jules. This assistant will empower developers by automating common coding tasks, integrating seamlessly with their GitHub workflow, and providing intelligent code generation and modification capabilities.

### 1.2. Inspiration (Google's Jules)
Google's Jules is an asynchronous AI coding agent designed to help developers with tasks like writing code, fixing bugs, updating dependencies, and more, directly within their existing workflows. Key aspects of Jules that inspire this project include its GitHub integration, plan-before-execution model, test validation, and innovative features like audio summaries.

### 1.3. Core Value Proposition
Jules-MVP aims to provide developers with a powerful, secure, and intuitive AI partner that:
*   Reduces toil by automating repetitive coding tasks.
*   Improves code quality through AI-assisted generation and testing.
*   Accelerates development cycles.
*   Integrates naturally into the GitHub ecosystem.

### 1.4. Target Users
Software developers of all levels who use GitHub for version control and collaboration, particularly those working with Python and JavaScript/TypeScript projects initially.

---

## 2. Preamble for AI Code Editor/Writer

**To the AI Coding Assistant:**

This document is your **master specification** for building the Jules-MVP. Your primary objective is to implement the features outlined herein, adhering to the revised 6-phase MVP plan. The goal is an MVP that mirrors the core user experience and key differentiating features of Google's Jules, emphasizing **secure task execution via containerization, seamless GitHub integration (including webhook triggers), and distinctive features like audio summaries** from an early stage.

### 2.1. Core Principles for AI Implementation
1.  **Adherence to Phased Plan:** Strictly follow the task breakdowns within each phase detailed in `/plans/phaseX/phaseX.md`.
2.  **Security First:** Prioritize secure sandboxing of all code execution (cloning, dependency installation, analysis, testing) using containerization (Docker) from MVP Phase 2 onwards. Implement best practices for secret management.
3.  **User Experience Parity:** Focus on delivering a workflow (UI and GitHub integration) that feels intuitive, powerful, and akin to the described Jules experience.
4.  **Modularity & Testability:** Design components (FastAPI routers, React components, worker tasks) for reusability and ensure core logic is covered by unit and integration tests.
5.  **Environment Variable Management:** Strictly use environment variables for all secrets (API keys, Supabase keys, GitHub App credentials) and configurations that differ between environments. Provide comprehensive `.env.example` files for both frontend and backend.
6.  **Clear Logging & Error Handling:** Implement robust, structured logging (especially within worker tasks and API handlers) for debugging. Provide clear, user-friendly error messages via the UI and API responses.
7.  **Idempotency:** Where applicable (e.g., setup scripts, some API endpoints), design for idempotency.
8.  **Code Quality & Documentation:** Generate clean, well-commented code. Update inline documentation and this master document as features are implemented.

### 2.2. Key Technologies
*   **Frontend:** Vite, React (JavaScript, potentially TypeScript later), TailwindCSS, Supabase-js, Axios/Fetch.
*   **Backend:** Python 3.9+, FastAPI, Uvicorn, Pydantic.
*   **Task Queue & Workers:** Redis, RQ (Python Redis Queue) or Celery.
*   **Containerization:** Docker.
*   **Authentication & DB:** Supabase (Postgres, GoTrue for Auth).
*   **AI Orchestration:** LiteLLM (or direct SDK integration initially).
*   **LLM Providers (Initial focus):** OpenAI API compatible (e.g., GPT-3.5/4). (User can bring their own keys).
*   **TTS Service:** Cloud-based TTS API (e.g., Google Cloud TTS, Azure Cognitive Services Speech, ElevenLabs).
*   **Version Control:** Git, GitHub, GitHub Apps.
*   **CI/CD:** GitHub Actions.

### 2.3. Development Environment (GitHub Codespaces)
This project is designed to be developed within **GitHub Codespaces**.
*   A `.devcontainer/devcontainer.json` configuration will be provided to set up the necessary services (Python, Node.js, Docker-in-Docker, Redis, Supabase CLI tools) and extensions.
*   This ensures a consistent, reproducible development environment for all contributors (AI and human).
*   AI Coder: You should be able to leverage this pre-configured environment to execute build, run, and test commands.

---

## 3. System Architecture

### 3.1. High-Level Architecture Diagram (Mermaid)

```mermaid
graph TD
    subgraph User Interaction Channels
        UI[("💻<br>React/Vite/Tailwind Frontend<br>(localhost:5173 / Deployed URL)")]
        GH_APP[("🎣<br>GitHub App Webhook Listener<br>(Backend Endpoint)")]
    end

    subgraph Backend Services (FastAPI - Python)
        API[("🌐<br>API Endpoints<br>(REST & WebSocket)")]
        TQ[("🔄<br>Task Queue<br>(Redis + RQ/Celery)")]
        LLM_ORCH[("🧠<br>AI Orchestration Layer<br>(LiteLLM / SDKs)")]
        TTS_SVC[("🗣️<br>Audio Summary Service<br>(TTS API Wrapper)")]
        GH_APP_HANDLER[("⚙️<br>GitHub App Event Handler")]
    end

    subgraph Task Execution Environment
        WORKER[("👷<br>Containerized Worker(s)<br>(Python Process consuming from TQ)")]
        WORKER -- Manages --> CONTAINER[("📦<br>Ephemeral Docker Container<br>(Per Task: Git, Runtimes, Deps)")]
    end

    subgraph Data & Auth Stores
        SUPA_AUTH[("🔐<br>Supabase Auth<br>(GitHub OAuth, JWTs)")]
        SUPA_DB[("🗄️<br>Supabase Postgres DB<br>(User Data, Tasks, Keys, Logs)")]
    end

    subgraph External Services & APIs
        GITHUB_API[("🐙<br>GitHub REST API")]
        LLM_API[("💡<br>LLM Provider APIs<br>(OpenAI, Gemini, etc.)")]
        TTS_API[("🎤<br>Cloud TTS Provider API")]
    end

    %% User Flows
    UI -- 1. User Actions (Login, Prompt, View) --> API
    GH_APP -- 1b. GitHub Event (e.g., Label Added) --> GH_APP_HANDLER
    GH_APP_HANDLER -- Creates Task --> API

    %% Auth Flow
    API -- Interacts for Auth --> SUPA_AUTH
    UI -- Direct Auth Interaction --> SUPA_AUTH

    %% Task Processing Flow
    API -- 2. Enqueues Task (Task Details, User Context) --> TQ
    TQ -- 3. Dispatches Task --> WORKER
    WORKER -- 4. Launches --> CONTAINER
    CONTAINER -- Clones Repo via User Token --> GITHUB_API
    CONTAINER -- Installs Dependencies --> CONTAINER
    CONTAINER -- Code Analysis/Modification --> LLM_ORCH
    LLM_ORCH -- Calls --> LLM_API
    CONTAINER -- Runs Tests --> CONTAINER
    CONTAINER -- Git Commit/Push, Create PR via User Token --> GITHUB_API
    WORKER -- Requests Audio Summary --> TTS_SVC
    TTS_SVC -- Calls --> TTS_API
    WORKER -- 5. Reports Progress/Results --> TQ
    TQ -- Updates Task in DB --> SUPA_DB
    TQ -- Relays Status/Logs --> API
    API -- Streams to UI via WebSocket / Stores final --> SUPA_DB

    %% Data Storage
    API -- Stores/Retrieves Task Data --> SUPA_DB
    WORKER -- Stores/Retrieves Task Data via TQ/API --> SUPA_DB
    API -- Stores/Retrieves User Profile, Encrypted Keys --> SUPA_DB

    %% AI Coder Note: Ensure arrows clearly show data/control flow.
    %% Style definitions for clarity
    classDef userChannel fill:#E6E6FA,stroke:#8A2BE2,stroke-width:2px;
    classDef backendComponent fill:#E0FFFF,stroke:#008B8B,stroke-width:2px;
    classDef executionEnv fill:#FFFACD,stroke:#FFD700,stroke-width:2px;
    classDef dataStore fill:#FFE4E1,stroke:#DC143C,stroke-width:2px;
    classDef externalSvc fill:#F0FFF0,stroke:#2E8B57,stroke-width:2px;

    class UI,GH_APP userChannel;
    class API,TQ,LLM_ORCH,TTS_SVC,GH_APP_HANDLER backendComponent;
    class WORKER,CONTAINER executionEnv;
    class SUPA_AUTH,SUPA_DB dataStore;
    class GITHUB_API,LLM_API,TTS_API externalSvc;
```

### 3.2. Component Descriptions

*   **Frontend (React/Vite/Tailwind):** Single-Page Application (SPA) for user interaction. Handles login, repository/branch selection, manual task prompting, display of plans, diffs, logs, test results, PR links, and an audio player for summaries.
*   **Backend (FastAPI - Python):**
    *   **API Endpoints:** Exposes RESTful and WebSocket endpoints for frontend communication and webhook handling.
    *   **GitHub App Event Handler:** Specific logic to process incoming webhooks from the GitHub App (e.g., task creation from labels).
    *   **Task Queue (e.g., Redis + RQ):** Manages asynchronous execution of coding tasks. Decouples API requests from long-running task processing. **Introduced in MVP Phase 2.**
    *   **AI Orchestration Layer (LiteLLM):** Abstraction layer for communicating with various LLM providers. Manages API calls, and potentially prompt templating and model selection logic.
    *   **Audio Summary Service (TTS):** A module that interfaces with a cloud TTS provider to convert text summaries of changes into audio files.
*   **Containerized Worker(s) (Python + Docker):**
    *   Processes that consume tasks from the Task Queue.
    *   Each worker launches an **Ephemeral Docker Container** per task. This container provides an isolated environment with necessary runtimes (Python, Node.js, Git) and tools.
    *   **Responsibilities within the container:**
        *   Cloning the target GitHub repository using the user's authenticated token.
        *   Installing project-specific dependencies (e.g., `pip install`, `npm install`).
        *   Executing code analysis scripts (potentially involving LLM calls for context understanding).
        *   Applying LLM-generated code modifications.
        *   Running automated tests defined in the repository.
        *   Committing changes, creating new branches, and opening Pull Requests on GitHub.
    *   Workers stream logs and status updates back to the API (via the Task Queue or a direct channel) for relay to the frontend.
*   **Data & Auth Stores (Supabase):**
    *   **Supabase Auth (GoTrue):** Handles user authentication via GitHub OAuth2, manages user sessions, and issues JWTs.
    *   **Supabase Postgres DB:** Stores user profiles, encrypted GitHub OAuth tokens, encrypted user-provided LLM API keys, task details (prompt, plan, status, PR URLs, audio summary links), and task logs. Row-Level Security (RLS) will be used extensively.
*   **External Services & APIs:**
    *   **GitHub REST API:** Used for listing repositories/branches, fetching file content, creating commits, branches, and Pull Requests.
    *   **LLM Provider APIs:** Interfaces to models like OpenAI's GPT series, Google's Gemini, Anthropic's Claude, etc., via the AI Orchestration Layer.
    *   **Cloud TTS Provider API:** Used by the Audio Summary Service to generate audio.

### 3.3. Technology Stack Summary
*   **Frontend:** React (with Vite), TailwindCSS, `supabase-js`, `axios` (or `fetch API`).
*   **Backend:** Python 3.9+, FastAPI, Uvicorn, Pydantic, `python-dotenv`, `supabase-py`, `requests`.
*   **Task Queue:** Redis, RQ (Python Redis Queue).
*   **Containerization:** Docker.
*   **AI/LLM:** LiteLLM, direct SDKs (e.g., `openai`).
*   **TTS:** SDK for chosen cloud provider (e.g., `google-cloud-texttospeech`, `azure-cognitiveservices-speech-sdk`).
*   **Database:** PostgreSQL (managed by Supabase).
*   **CI/CD:** GitHub Actions.
*   **Development Environment:** GitHub Codespaces with Docker-in-Docker.

---

## 4. Core Process Flow Diagram (Mermaid)

This diagram details the sequence of operations for a typical task.

```mermaid
sequenceDiagram
    actor User
    participant FE as Frontend (React SPA)
    participant BE_API as Backend API (FastAPI)
    participant TQ as Task Queue (Redis+RQ)
    participant Worker as Containerized Worker
    participant Container as Ephemeral Docker Container
    participant LLM as LLM Service (via LiteLLM)
    participant GitHub as GitHub API
    participant Supabase as Supabase (Auth & DB)
    participant TTS as TTS Service

    alt UI-Initiated Task
        User->>+FE: 1. Logs in (GitHub OAuth)
        FE->>+Supabase: Handles OAuth flow
        Supabase-->>-FE: Session (JWT)
        User->>FE: 2. Selects Repo/Branch, Enters Prompt
        FE->>+BE_API: 3. POST /api/tasks (Prompt, Repo, Branch, JWT)
        BE_API->>+Supabase: 4. Store GitHub Token (if new/refreshed from user session)
        Supabase-->>-BE_API: Confirmation
        BE_API->>Supabase: 5. Create Task Record (status: queued)
        BE_API->>+TQ: 6. Enqueue Task (task_id, details)
        TQ-->>-BE_API: Ack
        BE_API-->>-FE: { task_id, status: "queued" }
        FE-->>User: Displays "Task Queued" (Subscribes to WebSocket)
    else GitHub App-Initiated Task
        User->>+GitHub: Applies "assign-to-jules-mvp" label to Issue/PR
        GitHub->>+BE_API: 1b. Webhook Event (label added)
        BE_API->>BE_API: 2b. Authenticate Webhook (GitHub App Secret)
        BE_API->>GitHub: 3b. Fetch Issue/PR details for context/prompt
        GitHub-->>BE_API: Issue/PR Data
        BE_API->>Supabase: 4b. Identify User (if possible, or use App installation context)
        BE_API->>Supabase: 5b. Create Task Record (status: queued, from webhook)
        BE_API->>+TQ: 6b. Enqueue Task (task_id, details)
        TQ-->>-BE_API: Ack
        BE_API-->>-GitHub: HTTP 200 OK to Webhook
    end

    TQ->>+Worker: 7. Dispatch Task (task_id)
    Worker->>Worker: 8. Log "Starting Task" (stream to TQ/BE_API)
    Worker->>+Container: 9. Launch Ephemeral Docker Container
    Container->>GitHub: 10. Git Clone Repo (using User's GitHub Token via Worker)
    GitHub-->>Container: Repository Code
    Container->>Container: 11. Install Dependencies (npm/pip)
    Worker->>Worker: 12. Log "Dependencies Installed"

    Worker->>Container: 13. Request Code Analysis / Context Prep
    Container->>+LLM: 14. Send Prompt + Context -> Get Plan
    LLM-->>-Container: Plan (structured)
    Worker->>Worker: 15. Log "Plan Received"
    Worker->>BE_API: 16. Update Task with Plan (via TQ or direct)
    BE_API->>Supabase: Store Plan
    BE_API-->>FE: (WebSocket) Stream Plan to UI

    alt UI-Initiated Task (Approval Step)
        FE-->>User: Displays Plan for Review
        User->>FE: 17. Approves Plan
        FE->>BE_API: POST /api/tasks/{id}/approve_plan
        BE_API->>Worker: (via TQ) Signal Plan Approved
    end

    Worker->>Container: 18. Request Code Generation (based on approved plan step)
    Container->>+LLM: 19. Send Plan Step + Context -> Get Code Diff/Changes
    LLM-->>-Container: Code Diff/Changes
    Container->>Container: 20. Apply Changes to Local Clone
    Worker->>Worker: 21. Log "Changes Applied"
    Worker->>BE_API: 22. Update Task with Diff (via TQ or direct)
    BE_API->>Supabase: Store Diff (optional)
    BE_API-->>FE: (WebSocket) Stream Diff to UI

    alt UI-Initiated Task (Approval Step)
        FE-->>User: Displays Diff for Review
        User->>FE: 23. Approves Diff
        FE->>BE_API: POST /api/tasks/{id}/approve_diff
        BE_API->>Worker: (via TQ) Signal Diff Approved
    end

    Worker->>Container: 24. Execute Tests
    Container->>Container: Runs test suite (pytest, npm test etc.)
    Worker->>Worker: 25. Log "Tests Completed" (Pass/Fail + Logs)
    Worker->>BE_API: 26. Update Task with Test Results
    BE_API->>Supabase: Store Test Results
    BE_API-->>FE: (WebSocket) Stream Test Results

    opt Tests Passed OR User Override
        Worker->>Container: 27. Git Commit & Push New Branch
        Container->>GitHub: Push changes
        GitHub-->>Container: Confirmation
        Worker->>Container: 28. Create Pull Request
        Container->>GitHub: Create PR
        GitHub-->>Container: PR URL
        Worker->>Worker: 29. Log "PR Created: [URL]"
        Worker->>BE_API: 30. Update Task with PR URL
        BE_API->>Supabase: Store PR URL
        BE_API-->>FE: (WebSocket) PR URL

        Worker->>+TTS: 31. Request Audio Summary (text summary of changes)
        TTS-->>-Worker: Audio File URL / Data
        Worker->>BE_API: 32. Update Task with Audio Summary URL
        BE_API->>Supabase: Store Audio URL
        BE_API-->>FE: (WebSocket) Audio URL
    end

    Worker->>Worker: 33. Log "Task Completed"
    Worker->>-TQ: 34. Task Finished (success/failure)
    TQ->>BE_API: Notify Task Completion
    BE_API->>Supabase: Update Final Task Status
    BE_API-->>FE: (WebSocket) Final Status

    alt GitHub App-Initiated Task (Post-Completion)
        BE_API->>GitHub: Post Comment to Issue/PR (Summary, PR Link, Audio Link)
    end
```

---

## 5. Functional Specifications (MVP & Beyond)

### 5.1. MVP Core Features (Based on Revised 6-Phase Plan)
*(AI Coder Note: These are the primary features to implement for the MVP. Detailed acceptance criteria will be in phase-specific plans.)*

*   **F1. User Authentication (Phase 1):** Secure login via GitHub OAuth using Supabase. Session management with JWTs.
*   **F2. Basic UI Shell (Phase 1):** Minimal frontend structure for login, and placeholders for future content.
*   **F3. Containerized Task Execution Foundation (Phase 2):**
    *   Backend worker capable of launching Docker containers for tasks.
    *   Basic task queue (e.g., Redis + RQ) for asynchronous processing.
*   **F4. Repository Operations within Container (Phase 3):**
    *   Secure cloning of selected GitHub repository into the container.
    *   Installation of project dependencies (Python/Node.js) within the container.
*   **F5. Core AI Workflow (Plan -> Diff) within Container (Phase 3):**
    *   User can submit a prompt via UI for the selected repo/branch.
    *   LLM generates a textual plan of changes (initial version, can be simple).
    *   UI displays the plan; user approves.
    *   LLM generates code modifications (diffs) based on the plan.
    *   UI displays diffs; user approves.
    *   Changes are applied to the code in the container.
*   **F6. Real-Time Logging via WebSockets (Phase 3):** Frontend displays live logs and status updates streamed from the worker/container.
*   **F7. Test Execution in Container (Phase 4):** Assistant runs repository's automated tests on the modified code within the container. Test results (pass/fail, logs) are captured and displayed.
*   **F8. Pull Request Creation (Phase 4):** If tests pass (or user confirms), assistant commits changes to a new branch and creates a GitHub PR. PR link is displayed.
*   **F9. Audio Summary of Changes (Phase 4):** After PR creation, an audio summary of the changes is generated (using a cloud TTS service) and made available to the user (e.g., playable in UI, link in PR comment).
*   **F10. GitHub App Webhook Trigger (Phase 5):**
    *   A GitHub App allows tasks to be initiated by adding a specific label (e.g., `assign-to-jules-mvp`) to a GitHub Issue or Pull Request.
    *   Backend processes these webhooks, creates, and enqueues tasks.
    *   (Optional for MVP Phase 5) Basic feedback (e.g., a comment) posted back to the GitHub Issue/PR with task status or PR link.
*   **F11. Basic Task Quotas/Rate Limiting (Phase 5):** Simple mechanism to limit tasks per user or concurrent tasks to manage resources (e.g., stored in Supabase, checked by API).
*   **F12. LLM Provider/Model Selection (Basic) (Phase 6):** UI allows users to select from a pre-defined list of LLM providers/models and input their API keys. Keys are stored securely (encrypted in Supabase). LiteLLM for abstraction.
*   **F13. UX Polish & Documentation (Phase 6):** General improvements to UI/UX. User-facing documentation for MVP features.

### 5.2. Key Differentiators (Inspired by Jules - To be achieved in MVP)
*   **Secure, Isolated Task Execution:** Using containers from an early phase.
*   **Seamless GitHub Workflow:** Both UI-driven and GitHub-event-driven (webhook) task initiation.
*   **Plan-Before-Execute Model:** Users review a plan from the LLM.
*   **Automated Testing:** Integration of repository's own tests.
*   **Audio Summaries:** A distinctive feedback mechanism.

---

## 6. Technical Specifications

*(AI Coder Note: Refer to these sections for specific implementation details of each component. Detailed API endpoint definitions, component props, etc., will be elaborated in phase-specific plans or dedicated design docs if needed.)*

### 6.1. Frontend (React/Vite/Tailwind)
*   **Framework/Build:** ViteJS with React (JavaScript initially, consider TS for Post-MVP).
*   **Styling:** TailwindCSS.
*   **State Management:** React Context API for global state (session, theme). Zustand or Redux Toolkit for more complex local/feature state if needed.
*   **HTTP Client:** `axios` or native `fetch`.
*   **WebSocket Client:** Native `WebSocket` API or a lightweight library.
*   **Key Components:**
    *   `AuthButton` (handles login/logout with Supabase).
    *   `RepoSelector` (fetches and displays user repos/branches).
    *   `TaskForm` (prompt input, LLM selection).
    *   `PlanViewer` (displays LLM-generated plan).
    *   `DiffViewer` (e.g., `react-diff-viewer` to display code changes).
    *   `LogPanel` (displays real-time logs from WebSocket).
    *   `TestResultsDisplay` (shows test output).
    *   `AudioPlayer` (simple HTML5 audio element for playing TTS output).
    *   `SettingsPage` (for managing LLM API keys).
*   **Authentication:** Uses `supabase-js` for GitHub OAuth flow and session management. JWT from Supabase session sent in `Authorization: Bearer <token>` header for backend API calls.
*   **Environment Variables:** `VITE_SUPABASE_URL`, `VITE_SUPABASE_ANON_KEY`.

### 6.2. Backend (FastAPI, Python)
*   **Framework:** FastAPI (Python 3.9+).
*   **Server:** Uvicorn (with Gunicorn for production Post-MVP).
*   **Data Validation:** Pydantic.
*   **Key Modules/Routers:**
    *   `auth_router.py`: Handles any backend-specific auth logic (mostly Supabase JWT validation).
    *   `github_router.py`: Endpoints for listing repos, branches (interacts with GitHub API).
    *   `tasks_router.py`: Endpoints for creating tasks (`POST /tasks`), getting task status (`GET /tasks/{id}`), approving plan/diff, WebSocket endpoint (`/ws/tasks/{id}/status`).
    *   `webhook_router.py`: Endpoint for receiving GitHub App webhooks.
    *   `llm_orchestration_service.py`: Manages interaction with LiteLLM/LLM SDKs.
    *   `tts_service.py`: Manages interaction with TTS provider.
    *   `task_queue_interface.py`: Logic for enqueuing tasks to RQ/Celery.
*   **Authentication:** Depends on Supabase JWT validation (see `get_current_user` dependency pattern).
*   **CORS:** Configured to allow requests from the frontend origin.
*   **Environment Variables:** `SUPABASE_URL`, `SUPABASE_ANON_KEY`, `SUPABASE_SERVICE_ROLE_KEY` (for admin actions if any), `GITHUB_APP_ID`, `GITHUB_APP_PRIVATE_KEY`, `GITHUB_WEBHOOK_SECRET`, `REDIS_URL`, API keys for default LLM/TTS if provided by the system.

### 6.3. Task Execution Environment (Containerized Workers)
*   **Container Runtime:** Docker.
*   **Base Worker Image(s):**
    *   A primary Docker image containing Python, Node.js, Git, and common build tools.
    *   This image will host the Python worker script that consumes tasks from the queue.
*   **Worker Script (Python):**
    *   Connects to Task Queue (Redis).
    *   Pulls task messages.
    *   For each task:
        *   **Launches a new ephemeral Docker container** using the Docker SDK (or a pre-configured `docker run` command).
        *   **Volume Mounts:** Securely mounts a temporary workspace for the cloned repo into the inner container.
        *   **Command Execution:** Uses `docker exec` or similar to run commands (git clone, npm install, pip install, test scripts, code modification scripts) *inside* the inner ephemeral container.
        *   **Communication:** Streams stdout/stderr from the inner container back to the worker, which then relays it (e.g., via Redis Pub/Sub or by updating task logs in DB) for WebSocket delivery.
        *   Manages lifecycle of the inner container (start, stop, cleanup).
*   **Security:**
    *   Inner containers run with least privileges.
    *   Network policies to restrict outbound connections from inner containers if possible (advanced).
    *   Resource limits (CPU, memory) on inner containers.
    *   Regular updates to base Docker images.
    *   Secure handling of secrets (GitHub tokens, API keys) passed to/used by the worker and inner container (e.g., via environment variables sourced securely, temporary injection).

### 6.4. Database (Supabase/Postgres)
*   **Provider:** Supabase (managed PostgreSQL).
*   **Key Data:** User profiles, encrypted GitHub OAuth tokens, encrypted user LLM API keys, task definitions, task statuses, task plans (structured), task logs, PR URLs, audio summary URLs.
*   **Security:** Row-Level Security (RLS) enforced for all tables containing user-specific data (users can only access their own records).
*   **Backups:** Managed by Supabase.
*   **Migrations:** Supabase CLI for schema migrations (`/supabase/migrations`).

### 6.5. Authentication (Supabase Auth with GitHub OAuth)
*   **Provider:** Supabase Auth.
*   **Method:** GitHub OAuth2.
*   **Frontend:** `supabase-js` handles the redirect flow, session management, and JWT retrieval.
*   **Backend:** Validates JWTs received from the frontend using `supabase-py` and Supabase Auth's `/user` endpoint or JWT public keys.
*   **GitHub Token Storage:** User's GitHub OAuth access tokens (scoped for repo access) are obtained during the OAuth flow and **must be stored encrypted** in the Supabase database (e.g., in the `users` or a dedicated `user_oauth_tokens` table), associated with the user. The backend retrieves and decrypts this token when needing to act on the user's behalf with GitHub.
    *   **Encryption:** Use `pgaudit` if available or application-level encryption with a key managed securely by the backend (e.g., from a secret manager, or an env var for simplicity in Codespaces dev).

### 6.6. GitHub Integration (API & GitHub App)
*   **User-Context Operations (via User OAuth Token):**
    *   Listing user repositories and branches.
    *   Cloning private/public repositories the user has access to.
    *   Fetching file contents.
    *   Creating branches.
    *   Committing changes.
    *   Creating Pull Requests.
    *   (Optional) Reading Issue/PR details if task is linked.
*   **GitHub App (for Webhook-driven tasks & broader permissions if needed):**
    *   **Permissions:**
        *   Read access to code (for cloning/analysis if not using user token for this).
        *   Read access to issues & pull requests (metadata, contents, comments).
        *   Write access to issues & pull requests (for posting comments).
        *   (Optional) Read access to organization members.
    *   **Webhook Events:** Subscribed to `issues` (label added/removed), `pull_request` (label added/removed).
    *   **Webhook Handler:** A dedicated FastAPI endpoint to receive, verify (using webhook secret), and process these events.
    *   **Authentication:** GitHub App authenticates as an installation to make API calls (e.g., posting comments back).

### 6.7. AI Orchestration (LiteLLM & LLMs)
*   **Abstraction Layer:** LiteLLM is recommended to provide a unified interface to various LLM providers (OpenAI, Gemini, Anthropic, etc.).
*   **LLM Interaction:**
    *   The containerized worker (or a service it calls) will use LiteLLM to send prompts and receive responses.
    *   Prompts will include:
        *   System message (defining the assistant's role/persona).
        *   User's natural language instruction.
        *   Relevant code context (files, snippets, AST info – see Phase B of Post-MVP for advanced context).
        *   Instructions for desired output format (e.g., plan steps in JSON, code diffs).
*   **Model Selection (MVP Phase 6):**
    *   Initially, may default to a single well-performing model (e.g., GPT-3.5-Turbo, GPT-4-Turbo if budget allows).
    *   Later, UI will allow users to select from a list of supported models and provide their own API keys.
*   **API Key Management:** User-provided API keys for LLMs will be stored encrypted in the Supabase database, associated with their user ID. The worker will retrieve and use these keys for calls via LiteLLM.
*   **Context Window Management:** Implement strategies (truncation, summarization, focused context selection) to handle LLM context window limitations. Initial MVP: simple truncation or selection of explicitly mentioned files.

### 6.8. Audio Summary Service (TTS)
*   **Functionality:** Converts a text summary of the coding task's outcome into an audio file.
*   **Integration:** A Python module/service in the backend (callable by the worker).
*   **TTS Provider:** Use a cloud TTS API (e.g., Google Cloud Text-to-Speech, Azure Cognitive Services Speech, ElevenLabs). Choice depends on quality, cost, and ease of integration.
*   **Input:** Text summary (e.g., "Added 3 unit tests for `calculate_total` and refactored the error handling. PR #123 created.").
*   **Output:** URL to the generated audio file (e.g., stored temporarily in cloud storage like Supabase Storage or S3, or streamed directly if provider supports).
*   **API Key Management:** API key for the TTS service managed as a backend environment variable.

### 6.9. Real-Time Communication (WebSockets)
*   **Technology:** FastAPI's native WebSocket support (built on Starlette).
*   **Purpose:** Stream logs, status updates, and intermediate results (like plan, diff) from the backend (originating from the worker) to the connected frontend client in real-time.
*   **Endpoint:** e.g., `/ws/tasks/{task_id}/status`.
*   **Message Flow:** Worker sends updates to Task Queue -> API reads from queue/receives updates -> API pushes to relevant WebSocket connection(s). (Alternatively, worker might publish to a Redis Pub/Sub channel that the API's WebSocket handler subscribes to).

---

## 7. Data Specifications

### 7.1. Database Schema (Supabase/Postgres)
*(AI Coder Note: Implement these tables using Supabase migrations. Ensure RLS policies are created.)*

| Table                 | Columns (Type, Constraints, Notes)                                                                                                                                                                                                                            | Description                                                                                                                                                                                                                                                                                                                                                     | RLS Policies (Examples)                                                                                                                                                                                                                                                                                                                    |
| --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **users**             | `id UUID PRIMARY KEY DEFAULT auth.uid() REFERENCES auth.users(id) ON DELETE CASCADE`, `github_user_id BIGINT UNIQUE`, `github_username TEXT`, `email TEXT`, `avatar_url TEXT`, `encrypted_github_token TEXT`, `created_at TIMESTAMPTZ DEFAULT NOW()`, `updated_at TIMESTAMPTZ DEFAULT NOW()` | Stores user profile information linked to Supabase Auth users. `encrypted_github_token` for user's GitHub OAuth access token.                                                                                                                                                                                                                                                        | `SELECT: auth.uid() = id`, `UPDATE: auth.uid() = id`                                                                                                                                                                                                                                                                       |
| **llm_api_keys**      | `id UUID PRIMARY KEY DEFAULT gen_random_uuid()`, `user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE`, `provider_name TEXT NOT NULL` (e.g., "openai", "anthropic"), `encrypted_api_key TEXT NOT NULL`, `key_nickname TEXT`, `created_at TIMESTAMPTZ DEFAULT NOW()`, `updated_at TIMESTAMPTZ DEFAULT NOW()` | Stores user-provided LLM API keys, encrypted.                                                                                                                                                                                                                                                                                                                       | `SELECT: auth.uid() = user_id`, `INSERT: auth.uid() = user_id`, `UPDATE: auth.uid() = user_id`, `DELETE: auth.uid() = user_id`                                                                                                                                                                                             |
| **tasks**             | `id UUID PRIMARY KEY DEFAULT gen_random_uuid()`, `user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE`, `repo_full_name TEXT NOT NULL` (e.g., "owner/repo"), `branch_name TEXT NOT NULL`, `prompt TEXT NOT NULL`, `llm_provider TEXT`, `llm_model TEXT`, `status TEXT NOT NULL DEFAULT 'queued'` (queued, processing, plan_review, diff_review, testing, creating_pr, completed, failed, cancelled), `plan_json JSONB`, `generated_diff TEXT`, `test_results_json JSONB`, `pr_url TEXT`, `new_branch_name TEXT`, `audio_summary_url TEXT`, `error_message TEXT`, `created_at TIMESTAMPTZ DEFAULT NOW()`, `updated_at TIMESTAMPTZ DEFAULT NOW()`, `started_at TIMESTAMPTZ`, `completed_at TIMESTAMPTZ` | Core table for tracking each coding task. `plan_json` stores the structured plan.                                                                                                                                                                                                                                                         | `SELECT: auth.uid() = user_id`, `INSERT: auth.uid() = user_id`, `UPDATE: auth.uid() = user_id (for cancellation only)`                                                                                                                                                                                                |
| **task_logs**         | `id BIGSERIAL PRIMARY KEY`, `task_id UUID NOT NULL REFERENCES tasks(id) ON DELETE CASCADE`, `timestamp TIMESTAMPTZ DEFAULT NOW()`, `level TEXT DEFAULT 'INFO'` (INFO, ERROR, DEBUG, WARN), `stage TEXT`, `message TEXT NOT NULL`                               | Stores detailed, timestamped log messages for each task, useful for debugging and real-time display. `stage` can be (cloning, dependencies, planning, diffing, testing, pr_creation).                                                                                                                                                                               | `SELECT: EXISTS (SELECT 1 FROM tasks WHERE tasks.id = task_id AND tasks.user_id = auth.uid())` (Users can only see logs for their own tasks). `INSERT: (Backend service role only)`                                                                                                                                        |
| **github_app_installations** | `id BIGINT PRIMARY KEY` (GitHub Installation ID), `account_id BIGINT`, `account_login TEXT`, `account_type TEXT` (User/Organization), `target_type TEXT`, `permissions JSONB`, `repository_selection TEXT`, `installed_at TIMESTAMPTZ DEFAULT NOW()`, `suspended_at TIMESTAMPTZ`, `is_active BOOLEAN DEFAULT TRUE` | Tracks GitHub App installations (if using GitHub App for webhooks). Necessary for mapping webhook events to users/orgs if tasks are not directly tied to a logged-in user's session (e.g., org-level triggers). This might be more complex than needed for initial MVP webhook. | `SELECT/INSERT/UPDATE/DELETE: (Backend service role or specific admin role only)`                                                                                                                                                                                                                                          |
| **task_quotas**       | `user_id UUID PRIMARY KEY REFERENCES users(id) ON DELETE CASCADE`, `daily_task_limit INT DEFAULT 5`, `daily_tasks_used INT DEFAULT 0`, `concurrent_task_limit INT DEFAULT 2`, `last_reset_date DATE DEFAULT CURRENT_DATE`                                  | Basic quota management per user.                                                                                                                                                                                                                                                                                                                                        | `SELECT: auth.uid() = user_id`, `UPDATE: (Backend service role only, or specific trigger on task creation)`                                                                                                                                                                                                              |

**Notes on Schema:**
*   `encrypted_github_token` and `encrypted_api_key`: These **must** be encrypted at the application layer before insertion or by using Postgres encryption functions with a securely managed key. Supabase Vault might be an option for managing these secrets if it's available and fits the architecture. For Codespaces dev, an env var for the encryption key is acceptable.
*   `plan_json`: Storing the plan as JSONB allows for querying and easier structured display on the frontend.
*   `task_logs.level` and `task_logs.stage`: Help categorize logs for filtering and display.

### 7.2. API Payload Examples

*   **Create Task (UI Initiated): `POST /api/tasks`**
    ```json
    {
      "repo_full_name": "bar191/jules",
      "branch_name": "main",
      "prompt": "Refactor the UserAuth class in auth_utils.py to use a singleton pattern.",
      "llm_provider": "openai", // Optional, defaults if not provided
      "llm_model": "gpt-4-turbo-preview" // Optional
    }
    ```
    *Response:*
    ```json
    {
      "task_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "status": "queued",
      "message": "Task successfully queued."
    }
    ```

*   **Create Task (Webhook Initiated): `POST /api/webhooks/github`** (Internal processing after GitHub event)
    *   GitHub sends its standard webhook payload. Backend parses it to extract necessary info (repo, issue/PR number for context, user who triggered if available, label name).
    *   Internal call similar to UI initiated task creation.

*   **Get Task Status: `GET /api/tasks/{task_id}`**
    *Response:*
    ```json
    {
      "task_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "user_id": "yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy",
      "repo_full_name": "bar191/jules",
      "branch_name": "main",
      "prompt": "Refactor...",
      "status": "completed", // or any other status
      "plan_json": { /* structured plan */ },
      "generated_diff": "--- a/file.py\n+++ b/file.py...",
      "test_results_json": { "passed": true, "summary": "All 25 tests passed." },
      "pr_url": "https://github.com/bar191/jules/pull/42",
      "audio_summary_url": "https://storage.example.com/audio/task_xyz.mp3",
      "created_at": "YYYY-MM-DDTHH:MM:SSZ",
      // ... other fields
    }
    ```

*   **Approve Plan Step: `POST /api/tasks/{task_id}/plan/approve`** (Example, might be more granular)
    ```json
    // Body might contain specific step IDs if approving parts of a plan
    {}
    ```
    *Response:* `HTTP 200 OK` or updated task status.

### 7.3. WebSocket Message Formats (`/ws/tasks/{task_id}/status`)
*   Server-to-Client messages will be JSON objects.
    *   **Log Message:**
        ```json
        {
          "type": "log",
          "timestamp": "YYYY-MM-DDTHH:MM:SS.sssZ",
          "level": "INFO",
          "stage": "cloning",
          "message": "Cloning repository bar191/jules..."
        }
        ```
    *   **Status Update:**
        ```json
        {
          "type": "status_update",
          "new_status": "processing_plan",
          "details": { /* optional context specific to the status */ }
        }
        ```
    *   **Plan Ready:**
        ```json
        {
          "type": "plan_ready",
          "plan_json": { /* structured plan */ }
        }
        ```
    *   **Diff Ready:**
        ```json
        {
          "type": "diff_ready",
          "diff_text": "--- a/file.py..."
        }
        ```
    *   **Test Results Ready:**
        ```json
        {
          "type": "test_results",
          "results_json": { "passed": true, "summary": "..." }
        }
        ```
    *   **PR Created:**
        ```json
        {
          "type": "pr_created",
          "pr_url": "https://github.com/bar191/jules/pull/42"
        }
        ```
    *   **Audio Summary Ready:**
        ```json
        {
          "type": "audio_summary_ready",
          "audio_url": "https://storage.example.com/audio/task_xyz.mp3"
        }
        ```
    *   **Task Completion:**
        ```json
        {
          "type": "task_completed",
          "final_status": "completed", // or "failed"
          "pr_url": "...", // if successful
          "error_message": "..." // if failed
        }
        ```

### 7.4. Environment Variables
*(AI Coder Note: Create `.env.example` files for both frontend and backend in their respective root directories. Ensure actual `.env` files are in `.gitignore`.)*

*   **Frontend (`src/.env.local.example`):**
    ```env
    VITE_SUPABASE_URL="http://localhost:54321" # For local Supabase CLI; replace with cloud URL for deployment
    VITE_SUPABASE_ANON_KEY="your_local_supabase_anon_key" # For local Supabase CLI; replace for deployment
    VITE_BACKEND_API_URL="http://localhost:8000/api" # For local backend; replace for deployment
    VITE_BACKEND_WEBSOCKET_URL="ws://localhost:8000/ws" # For local backend; replace for deployment
    ```

*   **Backend (`backend/.env.example`):**
    ```env
    # Supabase (local focus for dev, use cloud Supabase vars for deployment)
    SUPABASE_URL="http://localhost:54321"
    SUPABASE_ANON_KEY="your_local_supabase_anon_key"
    SUPABASE_SERVICE_ROLE_KEY="your_local_supabase_service_role_key" # For admin operations

    # Task Queue
    REDIS_URL="redis://localhost:6379/0"

    # GitHub App (for webhook integration - Phase 5)
    GITHUB_APP_ID="your_github_app_id"
    GITHUB_APP_PRIVATE_KEY_PATH="/path/to/your/private-key.pem" # Or GITHUB_APP_PRIVATE_KEY="-----BEGIN RSA PRIVATE KEY-----\n..."
    GITHUB_WEBHOOK_SECRET="your_github_webhook_secret"

    # LLM Configuration (defaults if system provides one, or for testing)
    DEFAULT_LLM_PROVIDER="openai"
    DEFAULT_OPENAI_API_KEY="sk-..." # For system-wide default, if any. Prefer user keys.

    # TTS Configuration
    TTS_PROVIDER="google" # e.g., "google", "azure", "elevenlabs"
    GOOGLE_APPLICATION_CREDENTIALS="/path/to/your/gcp-service-account.json" # If using Google TTS
    # AZURE_SPEECH_KEY="your_azure_speech_key"
    # AZURE_SPEECH_REGION="your_azure_speech_region"
    # ELEVENLABS_API_KEY="your_elevenlabs_api_key"

    # Application Secrets
    APP_SECRET_KEY="a_very_strong_random_secret_for_encryption_etc" # For encrypting GitHub tokens, LLM keys

    # Logging
    LOG_LEVEL="INFO"

    # CORS Origins (comma-separated for FastAPI)
    CORS_ALLOWED_ORIGINS="http://localhost:5173,https://your-deployed-frontend.com"
    ```
*   **Codespaces Secrets:** For development in GitHub Codespaces, these environment variables should be configured as Codespaces secrets for security. The `.devcontainer/devcontainer.json` can reference these secrets.

---

## 8. Implementation Plan: Phased Approach

This project will be developed in iterative phases, allowing for continuous integration, testing, and refinement. Each phase builds upon the previous one, culminating in a feature-rich MVP that closely mirrors Jules's core capabilities.

### 8.1. MVP Development Phases (Revised 6-Phase Plan)

*(AI Coder Note: Detailed tasks, acceptance criteria, and specific implementation notes for each phase will reside in `/plans/phaseX/phaseX.md`. This section provides the high-level overview and DoD for each MVP phase.)*

#### **Phase 1: Setup & Authentication UI Stub**
*   **Duration:** 1-2 Weeks
*   **Goal:** Establish foundational project structure, local development environments (Codespaces with Supabase CLI), implement core user authentication via GitHub OAuth (local Supabase), and a basic UI shell. Basic CI.
*   **Key Deliverables:**
    *   GitHub repository `bar191/jules` initialized with directory structure (`/docs`, `/plans`, `/src`, `/backend`, `.github/workflows`, `.devcontainer`).
    *   `.devcontainer` configured for Codespaces (Python, Node, Docker, Redis, Supabase CLI tools).
    *   Local Supabase instance operational via Supabase CLI. GitHub OAuth provider configured for local Supabase.
    *   Basic FastAPI backend with a `/ping` endpoint and Supabase JWT validation dependency.
    *   Basic Vite/React/Tailwind frontend with login/logout via Supabase GitHub OAuth, displaying user info.
    *   Basic CI pipeline (GitHub Actions) for linting frontend/backend.
*   **Definition of Done:** Developers can open the project in Codespaces, start local Supabase, frontend, and backend. Users can log in via GitHub. CI passes. `phase1.md` completed.

#### **Phase 2: Containerized Worker & Basic Task Queue**
*   **Duration:** 2-3 Weeks
*   **Goal:** Implement the backend infrastructure for secure, isolated task execution using Docker containers and a task queue.
*   **Key Deliverables:**
    *   Task Queue (Redis + RQ) integrated into the FastAPI backend. API endpoint for enqueuing a dummy task.
    *   Python worker process that consumes tasks from the queue.
    *   Worker can launch an ephemeral Docker container using a base image (Python, Node, Git).
    *   Worker can execute a simple script (e.g., `git --version`, `echo 'hello from container'`) inside the container and retrieve its output/logs.
    *   Basic mechanism for worker to stream logs/status back to the API (e.g., via Redis Pub/Sub or updating task in DB).
*   **Definition of Done:** Backend can enqueue a task. A worker picks it up, launches a Docker container, runs a simple command inside, captures output, and the container is cleaned up. `phase2.md` completed.

#### **Phase 3: Core Workflow (Clone, Plan, Diff, Logs) in Container**
*   **Duration:** 3-4 Weeks
*   **Goal:** Implement the central AI-driven coding workflow (repo cloning, dependency installation, LLM plan generation, LLM diff generation) *within the containerized environment*. Implement real-time logging to UI.
*   **Key Deliverables:**
    *   Frontend UI for repo/branch selection and prompt input.
    *   `POST /api/tasks` endpoint fully functional for UI-initiated tasks.
    *   Worker, inside the container:
        *   Clones the selected GitHub repository using the user's stored (encrypted) GitHub token.
        *   Detects and installs project dependencies (`requirements.txt`, `package.json`).
        *   Sends prompt + basic code context (e.g., user-specified files or simple heuristics) to an LLM (via LiteLLM, default provider) to generate a textual plan.
        *   Upon (simulated or basic UI) approval, sends plan step + context to LLM to generate code diffs.
        *   Applies diffs to the cloned code within the container.
    *   Frontend displays the textual plan and code diffs (using `react-diff-viewer`).
    *   Real-time logs and status updates from the worker/container are streamed via WebSockets to the frontend.
*   **Definition of Done:** User can select a repo, enter a prompt, see a plan, approve, see a diff, approve, and changes are applied in the container. Logs stream to UI. `phase3.md` completed.

#### **Phase 4: Tests, Commit, PR Creation & Audio Summary**
*   **Duration:** 2-3 Weeks
*   **Goal:** Add automated test execution, Git operations for PR creation, and the audio summary feature.
*   **Key Deliverables:**
    *   Worker, inside the container:
        *   Executes the repository's test suite (e.g., `npm test`, `pytest`) on the modified code.
        *   Captures test results (pass/fail, logs).
    *   Frontend displays test results.
    *   If tests pass (or user confirms):
        *   Worker, inside the container: Commits changes to a new, uniquely named branch. Pushes branch to GitHub.
        *   Worker: Creates a Pull Request on GitHub using the GitHub API. PR description includes task summary.
    *   Frontend displays PR link.
    *   Backend Audio Summary Service generates an audio recap of changes using a cloud TTS API.
    *   Frontend provides a way to play the audio summary.
*   **Definition of Done:** End-to-end flow from prompt to PR creation with test execution and audio summary is functional. `phase4.md` completed.

#### **Phase 5: GitHub App Webhook Trigger & Basic Quotas**
*   **Duration:** 2-3 Weeks
*   **Goal:** Enable task initiation directly from GitHub via a GitHub App and webhooks. Implement basic task quotas.
*   **Key Deliverables:**
    *   A GitHub App created for the project with necessary permissions (read code, read/write issues/PRs for comments).
    *   Backend webhook endpoint to receive and verify events from the GitHub App (e.g., issue labeled `assign-to-jules-mvp`).
    *   Logic to parse webhook payload, extract context (e.g., issue content as prompt, repo details), and create/enqueue a task.
    *   (Optional) Basic comment posted back to the GitHub issue/PR with task status updates or final PR/audio link.
    *   Basic task quota system implemented (e.g., X tasks per day per user, Y concurrent tasks per user), enforced by the API.
*   **Definition of Done:** Tasks can be triggered by labeling a GitHub issue. Basic quotas are enforced. `phase5.md` completed.

#### **Phase 6: LLM Selector, UX Polish, Docs & MVP Launch**
*   **Duration:** 2-3 Weeks
*   **Goal:** Add LLM provider/model selection, polish the overall user experience, complete documentation, and prepare for an MVP "launch" (e.g., open to internal testers or public beta).
*   **Key Deliverables:**
    *   Frontend UI allows users to select LLM provider/model from a predefined list.
    *   Frontend UI for users to securely input and store their LLM API keys (encrypted in Supabase).
    *   Backend AI Orchestration Layer (LiteLLM) uses the selected provider and user's key.
    *   Comprehensive UX review and polish of the frontend application.
    *   Improved error handling and user feedback messages throughout the application.
    *   User-facing documentation (how to use, features, limitations, troubleshooting).
    *   Developer documentation (setup, architecture, contribution guidelines) updated.
    *   Final end-to-end testing of all MVP features.
*   **Definition of Done:** MVP is feature-complete according to the revised plan, well-polished, documented, and ready for initial users. `phase6.md` completed.

### 8.2. Detailed Phase Plans Location
Detailed, actionable plans for each phase (e.g., `phase1.md`, `phase2.md`, etc.) will be located in the `/plans/` directory of the `bar191/jules` repository. Each phase plan will break down its key deliverables into specific tasks, sub-tasks, estimated effort, assigned roles (if applicable), and detailed acceptance criteria.

---

## 9. Post-MVP Roadmap (Future Enhancements)
*(This section outlines features to be considered after the successful delivery of the revised 6-Phase MVP. It's drawn from previous discussions and aims for a truly advanced, Jules-like system.)*

### Tier 1: Core Robustness & Intelligence
1.  **Advanced Context Loading & Code Understanding:** AST parsing, symbol indexing, RAG with project docs.
2.  **Interactive & Editable Multi-Step Planning UI:** Granular control over LLM-generated plans.
3.  **Sophisticated LLM Prompt Engineering & Strategy Layer:** Task-specific prompt templates, dynamic construction.

### Tier 2: User Experience & Operational Polish
4.  **Advanced Task Failure Handling & Retry Mechanisms:** Better diagnostics, user-initiated retries.
5.  **Iterative Refinement & Dialogue with LLM:** Conversational refinement of plans/code.
6.  **Persistent Task History & Detailed Logs UI:** Comprehensive browsing of past tasks.
7.  **Repository-Specific Configuration:** `.jules-mvp-config.yml` for custom behaviors.

### Tier 3: Advanced Capabilities & Ecosystem Integration
8.  **IDE Integration (VS Code, JetBrains):** Full-featured plugins.
9.  **User Telemetry & LLM Performance Analytics (Opt-in):** Data-driven improvement.
10. **Token Budgeting & LLM Cost Metering:** Transparency and control.
11. **Team Collaboration & Shared Task Management:** Features for organizational use.
12. **Multi-Language & Framework Support Expansion:** Beyond Python/JS.
13. **Integration with Issue Trackers (Jira, etc.).**
14. **Security Scans Integration.**

---

## 10. Security Considerations
*(AI Coder Note: Security is paramount. Continuously evaluate and implement security best practices.)*
*   **Input Validation:** Sanitize all user inputs (prompts, API parameters).
*   **Output Encoding:** Ensure proper encoding for data displayed in the UI to prevent XSS.
*   **Authentication & Authorization:**
    *   Robust JWT validation.
    *   Strict RLS policies in Supabase.
    *   Secure storage and handling of GitHub OAuth tokens (encrypted at rest, short-lived in memory).
    *   Secure storage and handling of user LLM API keys (encrypted at rest).
*   **Container Security (Task Execution Environment):**
    *   Run containers with minimal privileges (non-root user).
    *   Use read-only file system mounts where possible.
    *   Limit container capabilities.
    *   Network policies to restrict outbound connections from containers.
    *   Scan base Docker images for vulnerabilities.
    *   Resource limits (CPU, memory, execution time) per container/task.
    *   Ensure thorough cleanup of containers and temporary workspaces.
*   **Dependency Management:** Regularly scan project dependencies (frontend, backend, worker) for vulnerabilities (e.g., `npm audit`, `pip-audit`, GitHub Dependabot).
*   **Secret Management:**
    *   Use environment variables for all secrets.
    *   In Codespaces, use repository/org secrets.
    *   For deployment, use the hosting platform's secret management solution.
    *   Encryption key for application-level encryption (`APP_SECRET_KEY`) must be strong and managed securely.
*   **GitHub App Security:**
    *   Securely store GitHub App private key and webhook secret.
    *   Validate webhook signatures.
    *   Request only necessary permissions for the GitHub App.
*   **Rate Limiting:** Protect API endpoints and resource-intensive operations.
*   **HTTPS:** Enforce HTTPS for all communications.

---

## 11. Testing Strategy
*   **Unit Tests:**
    *   Frontend: Jest/Vitest + React Testing Library for components and utility functions.
    *   Backend: Pytest for FastAPI route handlers, services, utility functions.
    *   Worker: Pytest for task processing logic, helper functions.
*   **Integration Tests:**
    *   Backend: Test interactions between API endpoints, services, task queue, and database (using a test database instance).
    *   Worker: Test interaction with Docker SDK, Git commands, LLM orchestration.
*   **End-to-End (E2E) Tests:**
    *   Use a framework like Playwright or Cypress to simulate user flows through the UI, interacting with the deployed (or locally fully composed) system.
    *   Cover key scenarios: login, task creation (UI & webhook), plan/diff review, PR generation, audio summary playback.
*   **AI Model Output Quality:** This is harder to automate. Will rely on:
    *   Manual review of generated plans and code.
    *   User feedback.
    *   Potentially using "golden datasets" of prompts and expected outputs for regression checking (advanced).
*   **CI Integration:** All tests run automatically in GitHub Actions on every push/PR.

---

## 12. Deployment Strategy (Post-MVP)
*(Initial MVP development will be local-first using Codespaces and local Supabase CLI. This section outlines considerations for a future public deployment.)*
*   **Frontend:** Static site hosting (Vercel, Netlify, GitHub Pages, AWS S3/CloudFront).
*   **Backend (FastAPI App, Workers, Redis):**
    *   Containerized deployment using Docker.
    *   Platform-as-a-Service (PaaS) like Fly.io, Render, Heroku (with Docker support).
    *   Orchestration with Kubernetes (AWS EKS, Google GKE, Azure AKS) for larger scale.
*   **Supabase:** Use a managed cloud Supabase project for production.
*   **Environment Configuration:** Use platform-specific mechanisms for managing environment variables and secrets.
*   **CI/CD:** GitHub Actions for automated build, test, and deployment pipelines.
*   **Monitoring & Logging:** Integrate with logging (e.g., ELK stack, Datadog, Sentry) and monitoring services.

---

## 13. Contribution Guidelines (for AI and Human Developers)
*   **Branching Strategy:** Gitflow-like (e.g., `main`, `develop`, `feature/xxx`, `fix/xxx`, `release/vx.y.z`). All work on feature branches.
*   **Commits:** Conventional Commits (e.g., `feat: ...`, `fix: ...`, `docs: ...`, `chore: ...`).
*   **Pull Requests:**
    *   All changes merged via PRs to `develop` (then `main` for releases).
    *   PRs must pass CI checks (linting, tests).
    *   PR descriptions should be clear, linking to relevant issues.
    *   Require at least one human review for significant changes (AI can assist with reviews).
*   **Code Style:**
    *   Frontend: Prettier, ESLint (configurations to be provided).
    *   Backend: Black, Ruff (or Flake8+isort) (configurations to be provided).
*   **Issue Tracking:** Use GitHub Issues. Label issues clearly (bug, feature, enhancement, chore, phase-1, etc.).
*   **Documentation:** Update this `project_details.md` and other relevant docs (`/plans`, inline code comments) as part of any feature development or change.
*   **AI Coder Interaction:**
    *   Provide clear, specific prompts to the AI, referencing sections of this document or phase plans.
    *   Review AI-generated code thoroughly for correctness, security, and adherence to style guides.
    *   Iterate with the AI, providing feedback for refinements.

---

## 14. Glossary
*   **AI Orchestration Layer:** Software component that manages interactions with one or more LLMs.
*   **AST:** Abstract Syntax Tree; a tree representation of the syntactic structure of source code.
*   **BYO Keys:** "Bring Your Own Keys"; users provide their own API keys for LLM services.
*   **CI/CD:** Continuous Integration/Continuous Deployment (or Delivery).
*   **Containerization:** Packaging software and its dependencies into a standardized unit (container) for development, shipment, and deployment (e.g., Docker).
*   **FastAPI:** A modern, fast (high-performance) web framework for building APIs with Python.
*   **GitHub App:** A first-class actor within GitHub that can be installed in organizations and user accounts, acting on its own behalf.
*   **GitHub OAuth App:** An application that users can authorize to access their GitHub data and act on their behalf.
*   **JWT:** JSON Web Token; a compact, URL-safe means of representing claims to be transferred between two parties.
*   **LiteLLM:** A library to simplify calling LLMs from different providers using a unified API.
*   **LLM:** Large Language Model.
*   **MVP:** Minimum Viable Product.
*   **PR:** Pull Request.
*   **RAG:** Retrieval Augmented Generation; an AI technique that combines a pre-trained LLM with an external knowledge retrieval system.
*   **RLS:** Row-Level Security; a database feature that restricts which rows a user can access or modify.
*   **RQ (Redis Queue):** A simple Python library for queueing jobs and processing them asynchronously with workers.
*   **Sandboxing:** Isolating an environment to run code securely without affecting the host system or other environments.
*   **SPA:** Single-Page Application.
*   **Supabase:** An open-source Firebase alternative providing database, auth, storage, and other backend services.
*   **Task Queue:** A system for managing and distributing asynchronous tasks to worker processes.
*   **TTS:** Text-to-Speech.
*   **Vite:** A fast frontend build tool and development server.
*   **Webhook:** An automated message sent from apps when something happens.

---

## 15. Document Version History

| Version | Date       | Author(s)      | Summary of Changes                                                                                                |
| :------ | :--------- | :------------- | :---------------------------------------------------------------------------------------------------------------- |
| 1.0     | `YYYY-MM-DD` | AI & User | Initial comprehensive draft, incorporating revised MVP plan, detailed specs, and AI coder instructions. |
|         |            |                |                                                                                                                   |

---
