# Post-MVP Development Plan: Evolving to a Full-Featured Jules-like Assistant

**Document Version:** 1.0
**Date:** `May 20 2025`

## 1. Introduction

This document outlines the phased development plan to evolve the "Jules-Inspired AI Coding Assistant" from its Minimum Viable Product (MVP) state into a comprehensive, custom version that includes the core features and functionality inspired by Google's Jules.

The MVP (Phases 1-5 of the initial plan) establishes the basic workflow: authentication, repo selection, prompt-based tasking, basic LLM interaction, test execution, and PR creation with real-time updates.

This Post-MVP plan focuses on enhancing:

*   **Robustness & Security:** Sandboxing, dependency management.
*   **Intelligence & Context Awareness:** Advanced code understanding, smarter LLM prompting.
*   **User Experience & Control:** Iterative planning, history, better error handling.
*   **Operational Polish:** Sophisticated branching, task management.
*   **Scalability & Extensibility:** Preparing the architecture for growth.

The ultimate goal is a powerful, reliable, and intelligent AI coding partner.

## 2. Guiding Principles for Post-MVP Development

*   **Iterative Enhancement:** Build upon the existing MVP foundation.
*   **User-Centricity:** Prioritize features that deliver the most value and best experience to developers.
*   **Modularity:** Design components to be as independent as possible for easier development, testing, and maintenance.
*   **Security First:** Ensure all new features, especially those involving code execution and dependency management, are designed with security as a top priority.
*   **Scalability:** Architect solutions that can scale to handle more users, larger repositories, and more complex tasks.
*   **Flexibility:** Be prepared to adapt to new LLM advancements and user feedback.

## 3. Post-MVP Phases Overview

This plan is broken down into logical phases. Each phase will have its own detailed sub-tasks, deliverables, and testing requirements.

*   **Phase A: Hardening the Core - Sandboxing & Dependencies (Foundation for Robustness)**
*   **Phase B: Enhanced Intelligence - Contextual Understanding & Planning**
*   **Phase C: Improving User Interaction & Control**
*   **Phase D: Operational Excellence & Scalability**
*   **Phase E: Advanced Features & Ecosystem Integration**

---

## Phase A: Hardening the Core - Sandboxing & Dependencies

**Goal:** Significantly improve the security, reliability, and environmental consistency of task execution.

**Duration Estimate:** 4-8 weeks

### A1. Secure Per-Task Sandboxing (Containerization)

*   **Description:** Transition from executing tasks directly on the backend server to running each task (cloning, dependency install, analysis, code modification, testing) within an isolated, ephemeral Docker container.
*   **Key Sub-Tasks:**
    1.  **Container Orchestration Research:** Evaluate lightweight options (Docker SDK, or a simple queue feeding Docker workers) vs. more robust (Kubernetes if future scale demands, but likely overkill now).
    2.  **Base Docker Image(s):** Create base Docker images pre-configured with common runtimes (Python, Node.js, Git) and tools. Allow for dynamic installation of specific versions if needed.
    3.  **Task Runner Service:** Develop a service/module in the backend responsible for:
        *   Receiving task details.
        *   Spinning up a new container instance for the task.
        *   Mounting the cloned repository (or cloning inside the container).
        *   Executing commands (dependency install, tests, etc.) inside the container.
        *   Streaming logs/output back to the main backend (for WebSocket relay).
        *   Collecting artifacts (e.g., test reports, modified code).
        *   Ensuring container cleanup post-task.
    4.  **Security Considerations:** Network isolation for containers, resource limits (CPU, memory), read-only mounts where possible, secure handling of secrets/API keys passed to the container.
    5.  **Integration:** Modify the existing task execution pipeline (from MVP Phase 4) to use this new containerized runner.
*   **Definition of Done:**
    *   All code execution related to a user's task (cloning, dependency install, LLM-driven modifications, testing) occurs within a new, isolated Docker container.
    *   Containers are properly cleaned up after task completion or failure.
    *   Logs and results from containerized execution are correctly streamed/reported back to the user.
    *   Security measures for containerization are implemented and documented.

### A2. Automated Dependency Resolution & Installation

*   **Description:** Integrate logic to automatically detect common dependency manifest files (e.g., `requirements.txt`, `pyproject.toml` with Poetry/PDM, `package.json`) within the cloned repository and install them inside the sandboxed container before further processing.
*   **Key Sub-Tasks:**
    1.  **Manifest File Detection:** Implement logic to scan the root of the cloned repository for known dependency files.
    2.  **Command Execution:** Based on detected files, execute the appropriate installation commands (e.g., `pip install -r requirements.txt`, `npm ci`) within the container.
    3.  **Error Handling:** Gracefully handle failures during dependency installation (e.g., missing system libraries, network issues) and report them to the user.
    4.  **Caching (Optional Optimization):** Explore strategies for caching dependencies to speed up repeated builds for the same dependency set (e.g., Docker layer caching, shared volume for caches if secure).
*   **Definition of Done:**
    *   The system automatically attempts to install dependencies for Python (pip, Poetry/PDM) and Node.js (npm/yarn) projects before code analysis or test execution.
    *   Dependency installation failures are clearly reported.
    *   This process occurs within the sandboxed container.

---

## Phase B: Enhanced Intelligence - Contextual Understanding & Planning

**Goal:** Make the assistant smarter by improving its understanding of the codebase and the way it plans and proposes changes.

**Duration Estimate:** 6-10 weeks

### B1. Advanced Context Loading & Code Understanding

*   **Description:** Implement more sophisticated techniques than just passing whole files to the LLM. Focus on identifying and providing only the most relevant code snippets and structural information.
*   **Key Sub-Tasks:**
    1.  **File Relevance Heuristics:** Develop initial heuristics based on the prompt (e.g., keywords, function names mentioned) to select a candidate set of files.
    2.  **AST (Abstract Syntax Tree) Parsing:**
        *   Integrate libraries for parsing code (e.g., `ast` for Python, `esprima`/`acorn` or TypeScript Compiler API for JS/TS).
        *   Extract key structural information: function/class definitions, imports, calls.
    3.  **Basic Symbol Indexing (In-Memory/Temporary):** For the selected files or a relevant subset, build a temporary index of symbols to understand relationships (e.g., where a function is defined vs. called).
    4.  **Context Chunking & Summarization:** Develop strategies for chunking large files or summarizing sections to fit within LLM context windows while retaining essential information.
    5.  **Contextual Prompt Augmentation:** Refine how the selected code context is formatted and presented to the LLM in the prompt.
*   **Definition of Done:**
    *   The system uses AST parsing and/or symbol indexing to select more targeted code context for the LLM.
    *   Strategies for handling large files/contexts are implemented.
    *   Demonstrable improvement in LLM's ability to understand and modify code based on more precise context.

### B2. Interactive & Editable Multi-Step Planning UI

*   **Description:** Evolve the planning phase from a simple text display to an interactive UI where users can review, understand, and potentially edit a structured, multi-step plan generated by the LLM.
*   **Key Sub-Tasks:**
    1.  **Structured Plan from LLM:** Refine prompting to encourage the LLM to return a plan in a structured format (e.g., JSON list of steps, each with a title, description, affected files/symbols, and intended action).
    2.  **Backend Plan Management:** Store the structured plan in the `tasks` table (or a related table).
    3.  **Frontend UI for Plan Display:**
        *   Display the plan as a list of collapsible/expandable steps.
        *   For each step, show details like description, target files/functions.
    4.  **Frontend UI for Plan Editing (Basic):** Allow users to:
        *   Approve/reject individual steps.
        *   Potentially reorder steps (if logically independent).
        *   Add notes or slightly modify the description of a step (this modified description could be fed back to the LLM for that step's execution).
    5.  **Backend Logic for Executing Edited Plan:** The backend must be able to execute the plan step-by-step, considering user approvals/edits.
*   **Definition of Done:**
    *   LLM generates a structured, multi-step plan.
    *   Frontend displays this plan in an interactive UI.
    *   Users can review and approve/reject (and potentially make minor edits to) individual steps in the plan before execution of those steps.
    *   The backend executes the plan according to user validation.

---

## Phase C: Improving User Interaction & Control

**Goal:** Enhance the overall user experience by providing more history, better feedback, and more granular control over the assistant's actions.

**Duration Estimate:** 4-6 weeks

### C1. Persistent Task History & Detailed Logs

*   **Description:** Implement a system for users to view their past tasks, including all relevant details and logs.
*   **Key Sub-Tasks:**
    1.  **Database Schema Enhancement:** Ensure `tasks` and `task_logs` tables (from MVP design) are robust enough to store all necessary information (prompt, structured plan, diffs applied, test results, PR link, status changes, detailed execution logs/timestamps).
    2.  **Backend API for History:** Create API endpoints to fetch a user's task history (paginated) and details for a specific task.
    3.  **Frontend UI for Task History:**
        *   A page listing all past tasks for the logged-in user.
        *   A detailed view for each task showing all stored information, including expandable logs and plan steps.
*   **Definition of Done:**
    *   Users can access a comprehensive history of their past tasks through the UI.
    *   Detailed logs and all relevant artifacts for each task are viewable.
    *   Database queries for history are optimized for performance.

### C2. Iterative Refinement & Dialogue with LLM (Basic)

*   **Description:** Allow a basic form of iteration. If a user is not satisfied with an LLM-generated plan or a specific code diff for a step, allow them to provide feedback or a clarifying instruction to regenerate that specific part.
*   **Key Sub-Tasks:**
    1.  **UI for Feedback:** Add "Regenerate" or "Refine" options next to plan steps or diff views. Allow a small text input for refinement instructions.
    2.  **Backend Logic for Refinement:**
        *   When a refinement is requested for a plan step, re-prompt the LLM for that step with the original context + user's refinement instruction.
        *   When a refinement is requested for a code diff, re-prompt the LLM (that generated the diff) with the original instruction for that change + user's feedback.
    3.  **State Management:** Update task state to reflect refinement iterations.
*   **Definition of Done:**
    *   Users can request regeneration/refinement of individual LLM-generated plan steps or code changes with additional instructions.
    *   The system handles these iterative requests and updates the UI accordingly.

---

## Phase D: Operational Excellence & Scalability

**Goal:** Improve the operational aspects of the assistant, making it more robust in real-world scenarios and preparing the backend for potential scaling.

**Duration Estimate:** 5-8 weeks

### D1. Sophisticated Branching & PR Management

*   **Description:** Implement more intelligent Git operations, especially around branch creation and PR details.
*   **Key Sub-Tasks:**
    1.  **Automated Branch Naming:** Generate unique, descriptive branch names (e.g., `jules-mvp/fix-user-login-flow-<task_id>`).
    2.  **Configurable PR Behavior:** Allow users (or have system defaults) to choose if PRs are created as drafts or ready-for-review.
    3.  **Enhanced PR Descriptions:** Automatically populate PR descriptions with more details: link to the task in the assistant, summary of the plan, key changes.
    4.  **Integration with Branch Protection (Read-Only):** If possible, check for branch protection rules on the target branch and warn the user if the PR might face immediate issues (this is complex and might require higher GitHub permissions).
*   **Definition of Done:**
    *   The assistant creates well-named feature branches for its changes.
    *   PRs have informative, automatically generated descriptions.
    *   Users have some control over PR creation (e.g., draft vs. final).

### D2. Advanced Task Failure Handling & Retry Mechanisms

*   **Description:** Make the system more resilient to failures and provide users with better options when things go wrong.
*   **Key Sub-Tasks:**
    1.  **Granular Error Reporting:** Improve error messages throughout the pipeline, providing more context and actionable advice.
    2.  **Task State Machine Refinement:** Enhance the backend task state machine to handle more failure states and potential recovery paths.
    3.  **Retry Logic (for transient errors):** Implement retry mechanisms for certain operations that might fail due to transient issues (e.g., network calls to LLM APIs or GitHub). Use exponential backoff.
    4.  **User-Initiated Retry/Replan:** Allow users to explicitly retry a failed task or a specific failed step, possibly after addressing an external issue or modifying the prompt/plan.
*   **Definition of Done:**
    *   The system provides clearer diagnostics for task failures.
    *   Automatic retries are implemented for specific transient errors.
    *   Users can manually trigger retries for failed tasks or steps.

### D3. Backend Scalability Preparations (Worker Queue)

*   **Description:** If not already done (e.g., if `BackgroundTasks` in FastAPI is becoming a bottleneck), refactor the backend task processing to use a dedicated message queue (e.g., Celery with Redis/RabbitMQ) and worker processes.
*   **Key Sub-Tasks:**
    1.  **Message Broker Setup:** Choose and configure a message broker.
    2.  **Task Definition for Queue:** Define long-running operations (cloning, LLM calls, testing) as tasks for the queue.
    3.  **Worker Implementation:** Create worker processes that consume tasks from the queue.
    4.  **Integration:** Modify the API endpoints to enqueue tasks instead of executing them directly or via simple background tasks.
    5.  **Monitoring for Queue & Workers:** Implement basic monitoring for queue length and worker status.
*   **Definition of Done:**
    *   Long-running, resource-intensive tasks are processed asynchronously via a message queue and dedicated workers.
    *   The system architecture is now more scalable to handle increased load by adding more workers.

---

## Phase E: Advanced Features & Ecosystem Integration

**Goal:** Introduce more advanced capabilities that significantly enhance the assistant's power and utility, and explore integrations with the broader developer ecosystem.

**Duration Estimate:** 8-12+ weeks (features here are larger and can be parallelized or further broken down)

### E1. Repository-Specific Configuration & Knowledge

*   **Description:** Allow the assistant to adapt its behavior and leverage knowledge specific to a given repository or organization.
*   **Key Sub-Tasks:**
    1.  **Configuration File:** Define a schema for a configuration file (e.g., `.jules-mvp-config.yml`) that can be placed in user repositories to specify:
        *   Preferred test commands/scripts.
        *   Environment variables needed for building/testing.
        *   Paths to include/exclude from LLM context.
        *   Project-specific LLM prompt prefixes or guidelines.
    2.  **Backend Logic to Parse & Use Config:** The assistant reads and applies these configurations during task execution.
    3.  **Retrieval Augmented Generation (RAG) - Basic:**
        *   Allow users to provide a corpus of project documentation (e.g., Markdown files, Confluence export).
        *   Implement a basic RAG pipeline: chunk and embed this documentation into a vector store.
        *   When processing a task, retrieve relevant documentation snippets and include them in the LLM prompt context.
*   **Definition of Done:**
    *   The assistant can read and utilize repository-specific configuration files.
    *   A basic RAG system is in place, allowing the LLM to be augmented with knowledge from user-provided documentation for a repository.

### E2. LLM Prompt Engineering & Strategy Layer

*   **Description:** Move beyond simple prompting to a more sophisticated system for constructing and selecting LLM prompts based on the task.
*   **Key Sub-Tasks:**
    1.  **Prompt Template Library:** Create a library of curated prompt templates for different types of coding tasks (e.g., "write unit tests for this function," "refactor this code for readability," "add a new feature based on these requirements," "document this module").
    2.  **Task Type Inference:** Develop heuristics or a simple classifier (possibly another LLM call) to infer the type of task from the user's natural language prompt.
    3.  **Dynamic Prompt Construction:** Based on the inferred task type and available context (code, RAG results, user config), dynamically construct the optimal prompt using the template library.
    4.  **Few-Shot Example Management:** Allow for the inclusion of few-shot examples in prompts for certain task types to improve LLM performance.
*   **Definition of Done:**
    *   A system for managing and applying task-specific prompt templates and strategies is implemented.
    *   Demonstrable improvement in the quality and consistency of LLM outputs across various task types due to better prompt engineering.

### E3. User Telemetry & LLM Performance Analytics (Opt-in)

*   **Description:** Collect anonymized data to understand how the assistant is being used, identify areas for improvement, and track LLM performance.
*   **Key Sub-Tasks:**
    1.  **Data Points Definition:** Define what data to collect (e.g., task types, duration, success/failure rates, LLM provider used, tokens consumed, user ratings if implemented). Ensure anonymization.
    2.  **Opt-in Mechanism:** Implement a clear opt-in mechanism for users regarding telemetry collection.
    3.  **Backend Data Collection & Storage:** Securely collect and store telemetry data (e.g., in a separate analytics database or time-series DB).
    4.  **Basic Dashboarding:** Create simple internal dashboards to visualize key metrics.
*   **Definition of Done:**
    *   An opt-in telemetry system is in place.
    *   Key usage and performance metrics are being collected and can be visualized.

### E4. IDE Integration (Proof of Concept)

*   **Description:** Develop a basic plugin for a popular IDE (e.g., VS Code) to allow users to invoke the assistant and view results without leaving their editor.
*   **Key Sub-Tasks:**
    1.  **IDE Choice & API Research:** Select an initial IDE (VS Code is common) and research its extension APIs.
    2.  **Basic Invocation:** Implement functionality in the plugin to send a prompt (with current file/selection context) to the assistant's backend.
    3.  **Displaying Results:** Show plans, diffs, and status updates within the IDE's UI elements (e.g., webviews, custom panels).
    4.  **Authentication Flow:** Handle authentication from within the IDE.
*   **Definition of Done:**
    *   A proof-of-concept IDE plugin allows users to initiate tasks and view high-level results for the selected IDE.
    *   Basic authentication from the IDE is functional.

## 4. Cross-Cutting Concerns (Addressed Throughout Phases)

*   **Security:** Continuous review and hardening of all components.
*   **Documentation:** Ongoing updates to user and developer documentation.
*   **Testing:** Expanding unit, integration, and end-to-end test suites for all new features.
*   **UX/UI Refinement:** Continuously improving the user interface based on new features and user feedback.
*   **Performance Optimization:** Monitoring and optimizing performance-critical parts of the system.

## 5. Conclusion

This Post-MVP plan provides a roadmap for transforming the initial Jules-inspired assistant into a significantly more powerful, intelligent, and robust tool. Each phase builds critical capabilities, moving closer to the vision of a comprehensive AI coding partner. Flexibility and iterative feedback will be key to navigating this complex development journey.
