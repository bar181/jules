## Preamble for AI Coder: Phase 1 - Setup & Authentication

**To the AI Coding Assistant:**

This document outlines Phase 1 of the "Jules-Inspired AI Coding Assistant" project. Your primary goal in this phase is to establish the foundational codebase, development environment, and a secure authentication mechanism using GitHub OAuth, all orchestrated locally with Supabase CLI. Focus on creating clean, well-documented, and testable code according to the specifications herein.

**Project Goal (Overall):** To build an AI coding assistant that integrates with GitHub, allowing developers to assign prompt-driven coding tasks, receive AI-driven code analysis, modifications, and automated pull-request creation.

**Phase 1 Specific Goal:** Initialize project structures (frontend and backend), set up local development environments with Supabase CLI, implement GitHub OAuth for user login, and verify this authentication flow end-to-end. Basic CI/CD will also be established.

**Key Technologies for Phase 1:**

*   **Frontend:** Vite, React (JavaScript/TypeScript), TailwindCSS, Supabase-js
*   **Backend:** Python 3.9+, FastAPI, Uvicorn, Supabase-py, python-dotenv
*   **Authentication:** Supabase Auth (with GitHub OAuth provider)
*   **Local Dev Orchestration:** Supabase CLI, Docker
*   **Version Control:** Git, GitHub
*   **CI/CD:** GitHub Actions

**Core Principles for AI Coder:**

1.  **Adherence to Plan:** Follow the tasks outlined in this document sequentially.
2.  **Modularity:** Create reusable components and functions where appropriate.
3.  **Security:** Pay close attention to secure handling of credentials and tokens, especially in environment variables and API calls. Use `.gitignore` effectively.
4.  **Clarity & Readability:** Write clean code with meaningful variable names and comments where necessary.
5.  **Testability:** While full TDD isn't mandated for every line, ensure core functionalities (like API endpoints and auth logic) are testable and basic tests are written as specified.
6.  **Idempotency (where applicable):** Consider if setup scripts or initialization commands can be run multiple times without adverse effects.
7.  **Environment Variable Management:** Strictly use environment variables for all sensitive data and configuration that differs between environments. Provide `.env.example` files.
8.  **Error Handling:** Implement basic error handling and logging, especially for API calls and authentication steps.

---

### Phase 1 Update & Change Tracking Checklist

This checklist helps track the status and any modifications to the `phase1.md` plan itself. It's for meta-tracking of the plan document.

| Item ID | Description                                      | Status (Not Started/In Progress/Completed/Blocked) | Last Updated | Notes/Changes Made                                                                 |
| :------ | :----------------------------------------------- | :------------------------------------------------- | :----------- | :--------------------------------------------------------------------------------- |
| P1.PLAN | Initial `phase1.md` draft created                | Completed                                          | `YYYY-MM-DD` | Initial version based on project requirements.                                     |
| P1.REV1 | Review of `phase1.md` by human supervisor/team   | ` `                                                | ` `          | ` `                                                                                |
| P1.AI.FDBK| AI Coder feedback incorporated (if any)          | ` `                                                | ` `          | ` `                                                                                |
| P1.LOCK | `phase1.md` plan locked for current iteration    | ` `                                                | ` `          | No further changes planned unless critical issues arise during implementation.     |
| P1.TASK.X | *[Repeat for each Task 1-10 in phase1.md]* Task X status | ` `                                                | ` `          | *Specific updates related to the execution of Task X, e.g., "Task 6 OAuth callback port confirmed"* |

---

### Phase 1 High-Level Architecture Diagram (Mermaid)

This diagram illustrates the key components and interactions specific to the authentication flow established in Phase 1, focusing on the local development setup.

```mermaid
graph LR
    subgraph User Browser
        A[React Frontend (localhost:5173)]
    end

    subgraph Local Supabase (Docker via Supabase CLI)
        C[Supabase Auth (GoTrue - localhost:54321/auth)]
        D[Supabase DB (Postgres - localhost:54322)]
        E[Supabase Studio (localhost:54323)]
        F[Supabase API Gateway (Kong - localhost:54321)]
    end

    subgraph Developer Machine
        B[FastAPI Backend (localhost:8000)]
    end

    subgraph External Services
        G[GitHub OAuth Service]
    end

    A -- 1. Login with GitHub (OAuth Init) --> C
    C -- 2. Redirect to GitHub for AuthZ --> G
    G -- 3. User Authorizes --> C
    C -- 4. Issues JWT to Frontend --> A
    A -- 5. Stores JWT, Displays User Info --> A
    A -- 6. API Call with JWT --> B
    B -- 7. Validates JWT with Supabase Client (against Local Supabase Auth) --> C
    C -- 8. Confirms JWT Validity --> B
    B -- 9. Returns User Data/Protected Resource --> A

    %% Optional DB interaction
    C -- On New User (Trigger) --> D[Profiles Table]
    B -- Can Query --> D

    %% Management
    Developer -- Manages --> E
    Developer -- Manages --> F

    style A fill:#D6EAF8,stroke:#3498DB
    style B fill:#D5F5E3,stroke:#2ECC71
    style C fill:#FCF3CF,stroke:#F1C40F
    style D fill:#FDEDEC,stroke:#E74C3C
    style E fill:#FEF9E7,stroke:#F39C12
    style F fill:#FEF9E7,stroke:#F39C12
    style G fill:#E8DAEF,stroke:#8E44AD
```

**Diagram Legend & Flow Description:**

1.  **User Interaction:** The developer (as a user) interacts with the React Frontend.
2.  **Login Initiation:** Frontend initiates GitHub OAuth login via the local Supabase Auth service.
3.  **GitHub Authorization:** Supabase Auth redirects the user to GitHub for authorization.
4.  **Token Issuance:** Upon successful GitHub authorization, GitHub redirects back to Supabase Auth, which then issues a JWT (JSON Web Token) to the Frontend.
5.  **Frontend Session:** The Frontend stores this JWT and can display user information derived from it.
6.  **Authenticated API Call:** The Frontend makes API calls to the FastAPI Backend, including the JWT in the `Authorization` header.
7.  **Backend JWT Validation:** The FastAPI Backend uses the `supabase-py` client (configured with local Supabase URL and anon key) to validate the received JWT by communicating with the local Supabase Auth service.
8.  **Validation Confirmation:** Local Supabase Auth confirms the JWT's validity.
9.  **Protected Resource Access:** If the JWT is valid, the Backend processes the request and returns the protected resource/data to the Frontend.
*   **Database Interaction (Optional for core auth, but part of plan):** The local Supabase Auth service can interact with the local Supabase Database (e.g., to store user profiles via triggers). The Backend can also query this database directly if needed (using appropriate credentials).
*   **Local Management:** The developer manages the local Supabase instance via Supabase Studio and the API Gateway.

---

### Key Data Structures & Payloads (Phase 1 Focus)

*   **Supabase Session Object (Frontend - `supabase-js`):**
    ```json
    // Example structure after successful login
    {
      "access_token": "eyJhbGciOiJIUzI1Ni...",
      "token_type": "bearer",
      "expires_in": 3600,
      "refresh_token": "some_refresh_token_string",
      "user": {
        "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx", // Supabase User ID (UUID)
        "aud": "authenticated",
        "role": "authenticated",
        "email": "user@example.com",
        "email_confirmed_at": "YYYY-MM-DDTHH:MM:SSZ",
        "phone": "",
        "confirmed_at": "YYYY-MM-DDTHH:MM:SSZ",
        "last_sign_in_at": "YYYY-MM-DDTHH:MM:SSZ",
        "app_metadata": {
          "provider": "github",
          "providers": ["github"]
        },
        "user_metadata": {
          // GitHub specific metadata (may vary based on scope)
          "avatar_url": "https://avatars.githubusercontent.com/u/xxxxxxx?v=4",
          "email": "user@example.com",
          "email_verified": true,
          "full_name": "Jane Doe",
          "iss": "https://api.github.com",
          "name": "Jane Doe",
          "preferred_username": "janedoe", // GitHub username
          "provider_id": "xxxxxxx", // GitHub user ID
          "sub": "xxxxxxx", // GitHub user ID
          "user_name": "janedoe" // GitHub username
        },
        "identities": [ /* ... */ ],
        "created_at": "YYYY-MM-DDTHH:MM:SSZ",
        "updated_at": "YYYY-MM-DDTHH:MM:SSZ"
      }
    }
    ```
*   **Backend Protected Endpoint Response (`/api/users/me`):**
    *   This will typically return a subset or all of the `user` object from the Supabase session, as validated by the backend. The exact structure depends on the `response_model` in FastAPI. For Phase 1, returning the `current_user` object as obtained from `supabase_client.auth.get_user()` is sufficient.
*   **Environment Variables:**
    *   **Frontend (`src/.env.local`):**
        *   `VITE_SUPABASE_URL`: e.g., `http://localhost:54321`
        *   `VITE_SUPABASE_ANON_KEY`: `<local_anon_key_from_supabase_start>`
    *   **Backend (`backend/.env`):**
        *   `LOCAL_SUPABASE_URL`: e.g., `http://localhost:54321`
        *   `LOCAL_SUPABASE_ANON_KEY`: `<local_anon_key_from_supabase_start>`
        *   `LOCAL_SUPABASE_SERVICE_ROLE_KEY`: `<local_service_role_key_from_supabase_start>` (if backend needs admin privileges for Supabase, not strictly for `get_user` with anon key).

---

### Critical Success Factors for AI Coder in Phase 1:

*   **Local Supabase Integration:** Correctly initializing, starting, and configuring the local Supabase instance (including GitHub OAuth provider details) is paramount.
*   **Environment Variable Handling:** All Supabase URLs and keys MUST be managed via environment variables and NOT hardcoded. `.env` files must be in `.gitignore`.
*   **OAuth Callback URI:** The GitHub OAuth App's "Authorization callback URL" must precisely match the one expected by the local Supabase Auth service (e.g., `http://localhost:54321/auth/v1/callback`).
*   **JWT Propagation & Validation:** The JWT obtained by the frontend must be correctly passed in the `Authorization: Bearer <token>` header to the backend, and the backend must correctly use `supabase-py` (configured for the local Supabase instance) to validate this token.
*   **CORS Configuration:** The FastAPI backend must have CORS correctly configured to allow requests from the frontend's origin (e.g., `http://localhost:5173`).

This preamble should give the AI Coder a solid context and clear directives for tackling Phase 1.

---
