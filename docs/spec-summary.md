# Jules-Inspired AI Coding Assistant – MVP Design

This document outlines the **MVP design and architecture** for a Jules-inspired AI coding assistant. The assistant integrates tightly with GitHub to allow authenticated developers to select a repository and branch, assign a prompt-driven coding task, and receive AI-driven code analysis, modifications, and pull-request (PR) creation. The system uses a modern web stack (Vite/React/Tailwind on the front end) and a Python-based API backend (FastAPI) with Supabase for authentication and storage. Real-time status and progress updates are delivered via WebSockets or a comparable mechanism.

## System Architecture

&#x20;*Figure: Example high-level architecture. Users access the React/Tailwind frontend (running in the browser or a CDN) which communicates with the FastAPI backend. Key components include Supabase (Auth + Postgres DB), GitHub integration (OAuth and API calls), and the AI Orchestration layer. Real-time updates flow back to the UI via WebSockets or a similar channel.*

The architecture has the following main components:

* **Frontend (React/Vite/Tailwind)**: A single-page app that handles user login, repository selection, task prompting, and displaying results (diffs, logs, test results). It uses **Supabase Auth** for GitHub login and connects to the backend via REST and WebSocket APIs.
* **Backend (FastAPI)**: A Python-based API server that orchestrates tasks. FastAPI is chosen for its **high performance** and ease of building async APIs. Key backend roles include handling API requests (e.g. starting a task), managing Git operations, running AI model calls, and executing tests.
* **Supabase**: Provides managed Postgres storage and authentication. We use Supabase Auth with GitHub OAuth for login, and store user data and task metadata in Postgres. *Supabase is “an open source Firebase alternative” offering a Postgres database, Auth, instant APIs, and Realtime subscriptions.* This covers user profiles, stored API keys, task records, and logs.
* **GitHub Integration**: The backend uses GitHub’s REST API (and/or Git) to list user repos/branches, clone repositories, commit changes, and open PRs. For example, a PR is created via GitHub’s `POST /repos/{owner}/{repo}/pulls` endpoint. The assistant works asynchronously with GitHub workflows, reflecting Jules’s approach.
* **AI Orchestration Layer**: An “LLM Selector Agent” chooses which large language model (LLM) to use for a given task. Users can enter API keys and select from supported providers (OpenAI, Google Gemini, Anthropic, etc.). The system may also dynamically switch models based on task type (e.g. documentation tasks vs code generation). We can leverage techniques like **LiteLLM** (or similar) to standardize different model APIs under a common interface. This lets us “switch seamlessly between self-hosted LLMs and externally hosted models” by converting inputs/outputs to a unified format.
* **Task Execution Environment**: For running code analysis, modifications, and tests, the backend will clone the repo to a **sandboxed environment**. In the MVP, this can be a containerized workspace or isolated directory on the backend server. (A future enhancement is to launch ephemeral VMs or containers per task for stronger isolation.)
* **Real-Time Updates (WebSocket)**: The backend streams progress, status updates, and results to the frontend via WebSockets (or SSE). FastAPI natively supports WebSocket endpoints for bidirectional communication (Starlette under the hood). Jules similarly “helps developers ... stay informed through real-time updates” on task progress. Our UI will connect to a WebSocket (or an e2b/Fly.io channel) to receive logs and status in real time.

In summary, Figure 1 above illustrates how the user’s browser, front-end app, and backend services (Supabase, GitHub, AI models) interact. Key data flows are: user login → fetch repos/branches → submit task prompt → backend clones code, runs analysis/LLM pipeline, updates code, executes tests → streams status/logs → frontend displays diffs/logs → creates PR in GitHub.

## Functional Specifications

The MVP provides the following core functions, mirroring Jules’s key features. Each function is described as a user story or feature:

* **User Authentication**: Developers log in with GitHub via Supabase Auth. On first use, users grant access to their repositories. (Supabase simplifies GitHub OAuth setup.) After login, the app fetches the user’s repo list.
* **Repository & Branch Selection**: The UI shows a list of the user’s GitHub repositories. The user selects a repository and a branch. The backend will then operate on a clone of that repo/branch. (E.g. GET `/api/repos` and `/api/branches?repo=...` endpoints.)
* **Prompt-Based Task Assignment**: The user enters a **task prompt** (natural language instruction), e.g. *“Add a test for function `calculateTax` in `utils.py`”*. The prompt is sent to the backend to initiate the coding task.
* **Task Planning (Preview)**: Upon receiving a prompt, the AI agent analyzes the codebase context and the request, then generates a **multi-step plan** of changes. This plan is presented to the user for review. (Following Jules’s approach, the plan details each intended change so developers can approve before execution.)
* **Codebase Analysis & Modification**: Once the user approves, the backend applies the changes. The AI model (selected by the LLM agent) generates code edits or diffs according to the plan. The backend updates the relevant files in the local clone. (All changes are staged for review.)
* **Test Execution and Validation**: If the repo has tests (e.g. via a script or CI), the assistant runs the tests automatically on the updated code. Results (pass/fail, logs) are captured. This ensures modifications don’t break the build before creating a PR.
* **Pull Request Creation**: If tests pass, the assistant commits the changes and uses the GitHub API to create a pull request on the selected branch. The PR includes the code diffs and a summary of changes. The user can then merge it via GitHub. (Alternatively, the app could create a PR draft or let the user push after manual review.)
* **Real-Time Status Updates**: Throughout the task, the frontend displays live logs and status (e.g. “Analyzing code...”, “Running tests...”). This uses WebSockets so the user sees progress in real time. (As Jules does, our assistant keeps the developer “informed through real-time updates”.)
* **Results Summary**: After task completion, the app shows a summary (similar to Jules’s “summary of all executed tasks”). This includes a recap of changes made, test results, and the PR link or diff. Optionally the assistant could speak an *audio summary* of what it did (a future enhancement).

These functional specs can be fleshed out in user-acceptance criteria. For example:

* *“Given a logged-in user, when they select a repo and enter a prompt, the system should fetch the repo, analyze code, propose changes, run tests, and open a PR with the expected modifications.”*
* *“The UI must allow selecting the LLM provider and inputting API keys, so the user can use their own keys for OpenAI, Gemini, etc.”*
* *“If any step fails (e.g. tests fail), the system should notify the user and provide logs.”*

## Frontend Technical Specifications

* **Tech Stack**: ViteJS with React for the UI, styled using TailwindCSS for utility-first styling. Vite provides fast hot-reload and bundling.
* **Authentication UI**: On launch, the app checks Supabase Auth state. If unauthenticated, show a “Login with GitHub” button. Use the Supabase JS client to handle OAuth redirect and session. (Supabase provides easy GitHub social login.)
* **State Management**: Use React Context or a state library (like Redux or Zustand) to manage user session, selected repo, task status, and LLM settings.
* **Repo/Branch Selection Component**: After login, fetch `GET /api/repos` to retrieve user’s GitHub repos (backend will use the GitHub token from Supabase to list repos). Display in a dropdown or list. On selecting a repo, fetch `GET /api/branches?repo=owner/repo` and let user pick a branch.
* **Task Prompt Form**: A text area where the user types the task prompt. Include options for selecting the LLM provider and model (dropdown of e.g. “OpenAI GPT-4”, “Gemini UltraPro”, “Claude Pro”, etc.). Allow entering or pasting API keys if not set yet.
* **LLM Provider Settings**: A settings page/modal where users can securely enter and store API keys for each provider. Keys can be saved in Supabase (encrypted or in Supabase Auth’s JWT) or held in browser (depending on security design).
* **Progress and Logs Panel**: A pane that shows a scrolling log of status messages from the backend (via WebSocket). Also show progress indicators (e.g. a spinner or progress bar) and step names (“Running analysis”, “Applying changes”, “Testing”).
* **Plan and Diff Viewer**: After analysis, display the AI-generated plan in text. If user approves, the diff viewer shows code changes (using a syntax-highlighting diff component, e.g. [react-diff-viewer](https://www.npmjs.com/package/react-diff-viewer)). Include “Apply Changes” and “Cancel” buttons.
* **Test Results UI**: Show test output (logs or summary) in a readable format. For example, a collapsible section that displays passed/failed tests. If tests fail, highlight errors.
* **PR Creation Confirmation**: After pushing changes, display the new PR link and a summary message. Possibly embed a short diff summary in the UI with a link to GitHub.
* **Real-time Updates**: Implement a WebSocket connection (or use a service like [E2B](https://e2b.dev/) or [Fly.io’s private networking](https://fly.io)) to receive real-time events from the backend. The frontend client listens and updates the logs/status UI. This allows the user to see each step as it happens.
* **UX Considerations**: Keep the UI responsive; use Tailwind to ensure mobile-friendly (though initial focus might be desktop developers). Ensure error messages are clear (e.g. “GitHub token expired” or “Model API key invalid”).

## Backend Technical Specifications

* **Tech Stack**: FastAPI (Python 3.9+) as the web framework, running under Uvicorn or Gunicorn. FastAPI is modern and high-performance, with async support for I/O-bound tasks (Git, HTTP calls).
* **API Endpoints**:

  * **Authentication**: (Mostly handled by Supabase) The backend trusts Supabase JWT for user identification. Endpoints may require the Supabase token in headers.
  * **Repository Listing**: `GET /api/repos` – returns the logged-in user’s GitHub repositories (calls GitHub API using user’s OAuth token stored in Supabase).
  * **Branch Listing**: `GET /api/branches?repo=...` – lists branches of the given repo (GitHub API).
  * **Create Task**: `POST /api/tasks` – body includes repo, branch, prompt, selected model/provider. Backend enqueues a new task and returns a task ID. The response: `{ "task_id": 123, "status": "queued" }`.
  * **Task Status/Logs**: `GET /api/tasks/{id}` – returns current status (queued, running, success, error) and final results (e.g. PR URL). Optionally, a WebSocket endpoint `/ws/tasks/{id}` for streaming logs.
  * **Commit and PR**: (part of task flow, not a standalone public endpoint) Backend internally calls GitHub’s `POST /repos/{owner}/{repo}/git/refs` and `POST /repos/{owner}/{repo}/git/commits` to create a branch and commit, then `POST /repos/{owner}/{repo}/pulls` to make the PR.
* **Supabase Integration**: Use the Supabase Python client or REST API to read/write from the Postgres database. Key tables (see Data Spec below) include `users`, `tasks`, and possibly `llm_keys` or `api_keys`. After user login, store their GitHub user ID and token (as an encrypted credential, or fetch from Supabase Auth headers).
* **Task Processing Pipeline**:

  1. **Clone Repository**: The backend clones the selected repo/branch to a local workspace (using `git` commands or a lib like GitPython). This ensures we work on the exact code snapshot.
  2. **Code Analysis**: Optionally parse or index code (e.g. build an AST or search for relevant functions) to provide context. For MVP, a simple approach is to pass file contents and prompt to the LLM.
  3. **Prompt to Plan**: The backend sends the user’s prompt and relevant code context to an LLM (via its API). The LLM returns a step-by-step plan (as a text response). The backend captures this plan.
  4. **User Approval**: The plan is sent to the frontend. The user reviews it; if changes are requested, the user can refine the prompt or modify the plan.
  5. **Apply Changes**: On approval, the backend asks the LLM (possibly a code-davinci model or similar) to generate concrete code changes (diffs or new code). It then writes these changes to the local clone.
  6. **Run Tests**: If the repo has tests (e.g. via `pytest` or an npm script), the backend executes them (perhaps in a subprocess or Docker). Collect stdout/stderr.
  7. **Create PR**: If tests pass (or even if they fail, but likely only on pass for MVP), commit the changes and use the GitHub API to create a pull request on the target branch. Record the PR URL.
* **LLM Provider Selector (Agent)**: Implement logic to choose an LLM API. The user can explicitly choose via the UI (overriding default). Additionally, implement simple heuristics: e.g., for doc generation tasks use one provider, for code tasks another. The **LiteLLM** approach (or a custom router) will let us treat all providers uniformly. The system must handle API key storage securely (e.g. in Supabase with RLS).
* **Background Execution**: Task processing should not block the main HTTP thread. Use FastAPI’s background tasks or a worker queue (Celery, RQ, or a custom process pool) to run each task. This ensures `POST /api/tasks` returns quickly with a task ID, while processing happens asynchronously.
* **WebSocket / Event Streaming**: The backend pushes status messages to clients via WebSocket. FastAPI can define a WS endpoint (e.g. `/ws/tasks/{id}`) that the frontend connects to. The backend, as it processes each step, sends messages like “Cloning repository…”, “Analyzing code…”, “Applying AI-suggested changes…”, “Running tests…”, and “Tests passed!” (or error) over the socket.
* **Error Handling**: If any step fails (Git error, model API failure, tests fail, etc.), the backend captures the error and updates the task status to “failed”, including an error message in the response and WebSocket stream. For example, if tests fail, we still create a PR to let the user fix issues (or we can abort – MVP design decision).
* **Logging and Observability**: Write logs of task activities (to console or to a log store). Critical info (task start/end times, user actions) should be in a database or monitoring tool. For MVP, simple timestamped logs (in the `task_logs` table) and WebSocket streaming suffice.
* **Security & Permissions**: Validate that the GitHub token used belongs to the logged-in user (via Supabase JWT claims). Ensure APIs require authentication (via a dependency that reads Supabase session). Use CORS to allow the frontend domain. Use HTTPS.

## Data Specifications

All persistent data is stored in Supabase (Postgres) or Supabase-provided storage. Key database tables and schemas:

| Table           | Columns (type)                                                                                                                                                                       | Description                                                                                                                                              |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **users**       | `id (uuid PK)`, `github_id (text)`, `github_login (text)`, `email (text)`, `created_at (timestamp)`                                                                                  | Stores user profile info. `github_id` links to GitHub account.                                                                                           |
| **api\_keys**   | `id`, `user_id (FK users)`, `provider (text)`, `encrypted_key (text)`, `created_at`                                                                                                  | Stores user’s LLM API keys (encrypted). Providers: e.g. “openai”, “google”, “anthropic”.                                                                 |
| **tasks**       | `id (uuid PK)`, `user_id (FK users)`, `repo (text)`, `branch (text)`, `prompt (text)`, `model (text)`, `status (text)`, `plan (text)`, `pr_url (text)`, `created_at`, `completed_at` | Each task request. Status = one of { queued, running, complete, error }. `plan` holds the AI-generated plan text. `pr_url` stores the resulting PR link. |
| **task\_logs**  | `id`, `task_id (FK tasks)`, `timestamp`, `message (text)`                                                                                                                            | Ordered log messages or events for each task (useful for replay and debugging).                                                                          |
| **files\_diff** | `id`, `task_id (FK tasks)`, `file_path (text)`, `diff_text (text)`                                                                                                                   | (Optional) Stores diffs for each file changed, to display in UI if needed.                                                                               |

*Table: Example database schema for MVP.*

**Row-Level Security (RLS)**: Supabase can enforce RLS so that users only see their own records. For example, `tasks.user_id = auth.uid()`. The frontend’s Supabase client will automatically apply the user’s JWT.

**API Payload Examples**:

* **Create Task (REST request)**:

  ```http
  POST /api/tasks
  Content-Type: application/json

  {
    "repo_full_name": "alice/example-repo",
    "branch": "develop",
    "prompt": "Add unit tests for function computeTotal in invoice.py",
    "provider": "openai",
    "model": "gpt-4"
  }
  ```

  *Response:*

  ```json
  {
    "task_id": "550e8400-e29b-41d4-a716-446655440000",
    "status": "queued"
  }
  ```

* **Task Status (REST response)**:

  ```http
  GET /api/tasks/550e8400-e29b-41d4-a716-446655440000
  ```

  *Response:*

  ```json
  {
    "task_id": "550e8400-e29b-41d4-a716-446655440000",
    "status": "complete",
    "plan": "- Found function computeTotal in invoice.py\n- Created new test file test_invoice.py\n- Wrote tests for edge cases...",
    "pr_url": "https://github.com/alice/example-repo/pull/42"
  }
  ```

* **WebSocket Messages**: Each message is a JSON object pushed from server to client. Example sequence:

  ```json
  { "task_id": "...", "timestamp": "...", "stage": "cloning", "message": "Cloning repository..." }
  { "task_id": "...", "stage": "analysis", "message": "Analyzing code for function computeTotal..." }
  { "task_id": "...", "stage": "planning", "message": "Generated plan, awaiting user approval." }
  { "task_id": "...", "stage": "applying_changes", "message": "Applying code changes..." }
  { "task_id": "...", "stage": "running_tests", "message": "Running unit tests..." }
  { "task_id": "...", "stage": "complete", "message": "Tests passed. Creating pull request." }
  ```

These specifications ensure consistent data storage and API design for the entire system.

## Implementation Plan and Milestones

We propose a phased implementation over several sprints. Each phase has specific deliverables and assigned roles:

| Milestone                                     | Duration  | Deliverables                                                                                                                                                                                                                                                                                                                                                                                | Roles/Owners                                                                                                                                                                                                                                                   |
| --------------------------------------------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Phase 1: Setup & Authentication**           | 1–2 weeks | • Project repos (frontend & backend) initialized<br>• Set up development environment (node, python)<br>• Configure Supabase project (Auth with GitHub OAuth)<br>• Implement basic FastAPI server and React app with Tailwind<br>• Verify user can log in via GitHub and see user info in UI                                                                                                 | **Backend Dev:** Configure Supabase and FastAPI auth <br> **Frontend Dev:** Set up React/Tailwind, implement login UI<br> **DevOps:** CI/CD pipeline (optional for MVP)                                                                                        |
| **Phase 2: Repo/Branch Integration**          | 1–2 weeks | • GitHub API integration on backend:<br>  – Endpoint to list repos and branches (using user’s token).<br>• Frontend UI for repo and branch selection.<br>• Backend clones a selected repo (dry-run clone).<br>• Display basic repository info in UI.                                                                                                                                        | **Backend Dev:** Use PyGithub or Git CLI to list repos/branches, clone.<br> **Frontend Dev:** Build selection dropdowns, handle API calls.<br> **QA:** Verify login and repo listing works.                                                                    |
| **Phase 3: Task Pipeline Core (Plan & Diff)** | 2–3 weeks | • Implement `POST /api/tasks` to accept prompt and enqueue task.<br>• Build AI analysis step: send prompt+context to LLM, receive plan.<br>• Frontend: Display plan text with “Approve/Cancel” buttons.<br>• If approved, call AI to generate code diff.<br>• Backend writes diff to files; record changes in `files_diff` table.<br>• Frontend: Show diff using syntax-highlighted viewer. | **AI/Backend Dev:** Integrate LLM API, implement planning & diff generation.<br> **Frontend Dev:** UI for prompt input, plan review, diff display.<br> **Backend Dev:** Data modeling (tasks, logs).<br> **QA:** Test simple prompts end-to-end (plan → diff). |
| **Phase 4: Test Execution & PR Creation**     | 1–2 weeks | • Backend: After code change, run automated tests (e.g. `npm test`, `pytest`). Capture output.<br>• If tests succeed, commit changes and use GitHub API to create PR (via `POST /pulls`).<br>• Webhook or direct response includes PR URL.<br>• Frontend: Display test results and PR link to user.<br>• Handle error cases (tests fail => show error).                                     | **Backend Dev:** Implement test runner (subprocess/Docker), Git commit + PR creation.<br> **Frontend Dev:** UI for showing test logs and PR link.<br> **QA:** Ensure test harness works with sample repos; PR is correctly created.                            |
| **Phase 5: Real-Time Updates & polishing**    | 1–2 weeks | • Implement WebSocket channel for streaming log/status messages during task execution (FastAPI WebSocket).<br>• Frontend: Connect WebSocket and display logs in real time.<br>• Implement LLM Provider Selector in UI (persist API keys).<br>• Error handling and user feedback improvements.<br>• Final end-to-end testing and documentation.                                              | **Backend Dev:** WebSocket endpoints, status updates.<br> **Frontend Dev:** Real-time log component, handle disconnections.<br> **QA:** Stress-test tasks, multi-task queue, failure modes.                                                                    |
| **Phase 6: Demo & Launch Prep**               | 1 week    | • Integration testing with multiple providers (OpenAI, Gemini, Anthropic).<br>• Setup deployment (Docker, hosting on Fly.io or similar).<br>• Prepare demo scenario and internal documentation.                                                                                                                                                                                             | **DevOps:** Deploy backend (FastAPI) and frontend. Ensure WebSocket support (e.g. Fly.io allows WS).<br> **Team:** Demo to stakeholders, gather feedback.                                                                                                      |

Each milestone is followed by a review. The team roles can overlap (e.g. a “full-stack” dev can cover front+back tasks). The **Project Manager (PM)** coordinates sprints and priorities.

## Future Feature Roadmap

Beyond the MVP, we envision these enhancements:

* **Audio Summary / Voice Assistant**: After completing a task, generate an audio summary of the changes (e.g. using a TTS API like Google Cloud Text-to-Speech or an open-source tool). This lets developers hear a spoken recap of what was done.
* **Advanced Task Planning**: Improve the planning phase by allowing iterative, hierarchical planning. For complex prompts, the agent could propose sub-tasks (like a to-do list) before implementation. This follows Jules’s concept of multi-step planning and could use larger-context LLMs (e.g. Gemini 3) or retrieval to break down tasks.
* **VM/Container-Based Execution**: For stronger isolation, run each task in its own ephemeral VM or container. This mirrors Jules’s cloud-VM approach and prevents side effects on the host. For example, use Docker or Kubernetes to spin up a Python/Node container per task, ensuring a clean environment.
* **Multi-Language & Framework Support**: Extend beyond Python/JavaScript. Support other languages (Java, Go, etc.) and testing frameworks. The system architecture remains the same; only the test execution script and context extraction need adaptation.
* **In-IDE Integration**: Develop plugins (VSCode, JetBrains) so developers can invoke the assistant directly within their IDEs, similar to how some tools integrate.
* **Collaboration & Sharing**: Allow users to share tasks or plans among teammates. E.g. one developer issues a task, another reviews the plan. Add user roles and permissions for an organization.
* **Knowledge Persistence**: Remember past tasks/prompts per repo (like a chat history), so the assistant can learn project-specific conventions over time.
* **Integration with Issue Trackers**: Link tasks to GitHub Issues or Jira tickets automatically. E.g. if the prompt mentions an issue ID, update the issue with the PR link when done.
* **Security Scans**: Run static analysis or security linting in the pipeline, alerting on vulnerabilities introduced.
* **Better LLM Orchestration**: Implement a full model management layer (like LISA) that lets organizations plug in custom or fine-tuned models, handle fallback if one API fails, and gather usage analytics.

This roadmap balances **developer productivity** features (planning, multi-language) with **technical robustness** (sandboxing, security) and **user experience** (audio, IDE plugins).

**Sources:** The design draws on Jules’s published description and industry examples. For instance, LiteLLM’s approach to unifying LLM APIs informs our “LLM selector” design. We leverage Supabase as an “open source Firebase alternative” for rapid backend setup, and FastAPI for high-performance async APIs. The final product aims to deliver a Jules-like coding workflow: asynchronous AI help integrated into the developer’s GitHub-centric workflow.
