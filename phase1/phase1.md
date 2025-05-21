/plans/phase1/phase1.md

# Phase 1: Setup & Authentication - Detailed Implementation Plan

**Project:** Jules-Inspired AI Coding Assistant (`bar181/jules`)
**Phase Duration:** 1-2 Weeks
**Primary Goal:** Establish foundational project structure, local development environments, and implement core user authentication via GitHub OAuth using a local Supabase instance, enabling users to log in and see basic user information, with CI/CD basics in place.

## Table of Contents

1.  [Prerequisites](#1-prerequisites)
2.  [Overall Deliverables for Phase 1](#2-overall-deliverables-for-phase-1)
3.  [Team Roles & Responsibilities (Assumed for MVP)](#3-team-roles--responsibilities-assumed-for-mvp)
4.  [Detailed Task Breakdown](#4-detailed-task-breakdown)
    *   [Task 1: Project Initialization & Version Control Setup](#task-1-project-initialization--version-control-setup)
    *   [Task 2: Development Environment Tooling Setup](#task-2-development-environment-tooling-setup)
    *   [Task 3: Initialize Local Supabase Environment](#task-3-initialize-local-supabase-environment)
    *   [Task 4: Initialize Frontend (Vite + React + TailwindCSS)](#task-4-initialize-frontend-vite--react--tailwindcss)
    *   [Task 5: Initialize Backend (FastAPI Project)](#task-5-initialize-backend-fastapi-project)
    *   [Task 6: Configure Supabase Auth (GitHub OAuth - Local Focus)](#task-6-configure-supabase-auth-github-oauth---local-focus)
    *   [Task 7: Implement Frontend Login Flow with Local Supabase](#task-7-implement-frontend-login-flow-with-local-supabase)
    *   [Task 8: Implement Backend Auth Verification with Local Supabase](#task-8-implement-backend-auth-verification-with-local-supabase)
    *   [Task 9: Basic CI/CD Pipeline Setup (GitHub Actions)](#task-9-basic-cicd-pipeline-setup-github-actions)
    *   [Task 10: Documentation, Review, and Phase 1 Wrap-up](#task-10-documentation-review-and-phase-1-wrap-up)
5.  [Definition of Done for Phase 1](#5-definition-of-done-for-phase-1)
6.  [Potential Risks & Mitigations](#6-potential-risks--mitigations)
7.  [Communication & Coordination](#7-communication--coordination)

---

## 1. Prerequisites

*   GitHub accounts for all team members.
*   Familiarity with Python/FastAPI, Node.js/React, Git, TailwindCSS, and Docker.
*   Agreement on Python (e.g., 3.9+) and Node.js (e.g., latest LTS) versions.

## 2. Overall Deliverables for Phase 1

*   Initialized GitHub repository (`bar181/jules`) with the specified directory structure.
*   Documented local development environment setup for both frontend and backend.
*   A locally running Supabase instance configured with GitHub OAuth.
*   A basic FastAPI backend server capable of:
    *   Running locally.
    *   Validating Supabase JWTs (from local Supabase).
    *   A protected endpoint returning authenticated user info.
*   A basic React/Vite/Tailwind frontend application capable of:
    *   Running locally.
    *   Handling GitHub login via the local Supabase instance.
    *   Displaying basic authenticated user information.
    *   Calling the protected backend endpoint.
*   A basic CI pipeline (GitHub Actions) for linting and/or basic build/test checks.
*   This `phase1.md` document completed and committed.

## 3. Team Roles & Responsibilities (Assumed for MVP)

*   **Backend Developer(s):** Supabase CLI setup, FastAPI server, backend auth logic, protected endpoints.
*   **Frontend Developer(s):** React app setup, TailwindCSS, frontend auth UI, Supabase client integration.
*   **DevOps/Lead Developer:** CI/CD, overall project structure, resolving integration issues. (Can be a developer wearing multiple hats).
*   **All Team Members:** Repository setup, environment setup, testing, documentation contributions.

## 4. Detailed Task Breakdown

Each step below is generally sequential; ensure prerequisites from previous tasks are met.

### Task 1: Project Initialization & Version Control Setup

*   **Action:**
    1.  Create the GitHub repository: `bar181/jules`.  DONE
    2.  Clone the repository locally. NOT REQUIRED
    3.  Initialize Git if not already done by GitHub: `git init`.  DONE
    4.  Create the core directory structure:
        ```
        bar181/jules/
        ├── .github/
        │   └── workflows/       # For GitHub Actions
        ├── backend/             # FastAPI application
        ├── docs/                # Core project details, resources
        ├── plans/               # Detailed phase plans
        │   └── phase1/
        │       └── phase1.md    # This document
        ├── src/                 # React frontend application (Vite)
        ├── supabase/            # For Supabase CLI config (created later)
        ├── .gitignore
        └── README.md
        ```
    5.  confirm a comprehensive `.gitignore` file includes all details for frontend backend, node, modules, .env, etc:

    6.  Create a basic `README.md` at the project root.
    7.  Commit and push the initial structure:
        ```bash
        git add .
        git commit -m "feat: Initial project structure and .gitignore"
        git push origin main # or your default branch
        ```
*   **Definition of Done:** GitHub repository `bar181/jules` exists with the defined initial directory structure and `.gitignore`. Code is pushed.

### Task 2: Development Environment Tooling Setup

*   **Action:**
    1.  **Install Core Tools:**
        *   Node.js (latest LTS version, e.g., v18 or v20). Verify with `node -v`.
        *   Python (e.g., 3.9+). Verify with `python3 -V` or `python -V`.
        *   Docker Desktop (or Docker Engine for Linux). Verify with `docker --version` and ensure Docker daemon is running (`docker ps`).
        *   Git (if not already installed). Verify with `git --version`.
    2.  **Install Supabase CLI:**
        ```bash
        npm install -g supabase
        supabase -v # Verify installation
        ```
    3.  **Documentation:** Create a section in the main `README.md` or a `docs/development_setup.md` outlining these installation steps and recommended versions.
*   **Definition of Done:** All required development tools (Node.js, Python, Docker, Git, Supabase CLI) are installed and their versions verified. Basic setup documentation is created.

### Task 3: Initialize Local Supabase Environment

*   **Action:**
    1.  Navigate to the project root (`bar181/jules/`).
    2.  Initialize Supabase project locally:
        ```bash
        npx supabase init
        ```
        (This creates the `supabase/` folder with `config.toml` and `migrations/`.)
    3.  Start the local Supabase services:
        ```bash
        npx supabase start
        ```
        (This will pull Docker images if it's the first time and start containers for Postgres, GoTrue (Auth), Kong (API Gateway), etc.)
    4.  Note down the local Supabase details from the output (API URL, DB URL, Studio URL, anon key, service_role key). These are crucial.
        *   Example Output:
            ```
            API URL: http://localhost:54321
            DB URL: postgresql://postgres:postgres@localhost:54322/postgres
            Studio URL: http://localhost:54323
            JWT Deno CLI URL: http://localhost:54321/functions/v1/_jwt
            anon key: <your_local_anon_key>
            service_role key: <your_local_service_role_key>
            ```
    5.  Verify access to Supabase Studio by opening the Studio URL (e.g., `http://localhost:54323`) in your browser.
*   **Definition of Done:** The `supabase/` directory is created and configured. Local Supabase Docker containers are running successfully. Supabase Studio is accessible. API URL and keys are noted.

### Task 4: Initialize Frontend (Vite + React + TailwindCSS)

*   **Action:**
    1.  Navigate to the frontend directory: `cd src/`.
    2.  Create a new Vite + React project (using JavaScript or TypeScript):
        ```bash
        # For TypeScript
        # npm create vite@latest . -- --template react-ts
        ```
    3.  Install project dependencies: `npm install`.
    4.  Install TailwindCSS and its peer dependencies:
        ```bash
        npm install -D tailwindcss postcss autoprefixer
        npx tailwindcss init -p
        ```
        (This creates `tailwind.config.js` and `postcss.config.js`.)
    5.  Configure `tailwind.config.js`:
        ```javascript
        // src/tailwind.config.js
        /** @type {import('tailwindcss').Config} */
        export default {
          content: [
            "./index.html",
            "./src/**/*.{js,ts,jsx,tsx}", // Adjusted to look inside src/src if main.jsx is there
          ],
          theme: {
            extend: {},
          },
          plugins: [],
        }
        ```
        Ensure the `content` path correctly points to your source files relative to the `tailwind.config.js` location. If `tailwind.config.js` is in `src/`, then paths like `./src/**/*.{js,jsx,ts,tsx}` are correct.
    6.  Add Tailwind directives to your main CSS file (e.g., `src/index.css` or `src/main.css`):
        ```css
        /* src/index.css */
        @tailwind base;
        @tailwind components;
        @tailwind utilities;
        ```
    7.  Import this CSS file in your main entry point (e.g., `src/main.jsx`).
    8.  Test the setup:
        *   Run the dev server: `npm run dev`.
        *   Modify `src/App.jsx` to include a Tailwind-styled element (e.g., `<h1 className="text-3xl font-bold text-blue-600">Hello Jules Frontend!</h1>`).
        *   Verify the page loads and styling is applied in the browser (default: `http://localhost:5173`).
*   **Definition of Done:** Frontend Vite+React project is initialized within `src/`. TailwindCSS is configured and working. The development server runs, and a sample styled component displays correctly.

### Task 5: Initialize Backend (FastAPI Project)

*   **Action:**
    1.  Navigate to the backend directory: `cd backend/`.
    2.  Create and activate a Python virtual environment:
        ```bash
        python3 -m venv venv
        source venv/bin/activate  # On Linux/macOS
        # venv\Scripts\activate    # On Windows
        ```
    3.  Install FastAPI, Uvicorn, and other necessary packages:
        ```bash
        pip install fastapi uvicorn[standard] python-dotenv supabase pytest httpx
        ```
    4.  Create the basic application structure:
        ```
        backend/
        ├── app/
        │   ├── __init__.py
        │   ├── main.py
        │   └── routers/
        │       └── __init__.py
        │   └── core/              # For config, dependencies
        │       └── __init__.py
        ├── tests/
        │   └── __init__.py
        ├── .env.example         # Example environment variables
        ├── requirements.txt
        └── venv/
        ```
    5.  Create `app/main.py`:
        ```python
        # backend/app/main.py
        from fastapi import FastAPI
        from fastapi.middleware.cors import CORSMiddleware

        app = FastAPI(title="Jules AI Coding Assistant Backend")

        # Configure CORS (adjust origins as needed for dev and prod)
        origins = [
            "http://localhost:5173", # Vite default frontend
            # Add deployed frontend URL later
        ]
        app.add_middleware(
            CORSMiddleware,
            allow_origins=origins,
            allow_credentials=True,
            allow_methods=["*"],
            allow_headers=["*"],
        )

        @app.get("/api/ping", tags=["Health Check"])
        async def ping():
            return {"message": "pong"}
        ```
    6.  Create `requirements.txt`:
        ```bash
        pip freeze > requirements.txt
        ```
    7.  Create a basic test in `tests/test_main.py`:
        ```python
        # backend/tests/test_main.py
        from fastapi.testclient import TestClient
        from app.main import app # Adjust import if main.py is elsewhere

        client = TestClient(app)

        def test_ping():
            response = client.get("/api/ping")
            assert response.status_code == 200
            assert response.json() == {"message": "pong"}
        ```
    8.  Run tests: `pytest`.
    9.  Run the development server: `uvicorn app.main:app --reload --port 8000`. Verify `/api/ping` in browser or with `curl`.
*   **Definition of Done:** FastAPI project is initialized. A basic `/api/ping` endpoint is functional and tested. CORS is configured for local frontend development. `requirements.txt` is generated.

### Task 6: Configure Supabase Auth (GitHub OAuth - Local Focus)

*   **Action:**
    1.  **On GitHub:**
        *   Go to Settings -> Developer settings -> OAuth Apps -> New OAuth App.
        *   Application name: e.g., "Jules Assistant Dev Local"
        *   Homepage URL: `http://localhost:5173` (your Vite frontend dev server URL).
        *   Authorization callback URL: `http://localhost:54321/auth/v1/callback` (This is the default auth callback for the local Supabase instance. Verify the port from `supabase start` output if different).
        *   Generate a new client secret. Copy the Client ID and Client Secret.
    2.  **In Local Supabase Studio:**
        *   Open your local Supabase Studio (e.g., `http://localhost:54323`).
        *   Navigate to Authentication (shield icon) -> Providers.
        *   Find GitHub in the list, expand it.
        *   Paste the Client ID and Client Secret obtained from GitHub.
        *   Enable the GitHub provider. Click Save.
    3.  **(Optional but Recommended) Create `profiles` table for user data:**
        *   In Supabase Studio -> SQL Editor -> New query.
        *   Execute:
            ```sql
            -- Create a table for public user profiles
            CREATE TABLE public.profiles (
              id UUID NOT NULL PRIMARY KEY REFERENCES auth.users(id) ON DELETE CASCADE,
              username TEXT,
              avatar_url TEXT,
              full_name TEXT,
              updated_at TIMESTAMPTZ DEFAULT NOW()
            );

            ALTER TABLE public.profiles ENABLE ROW LEVEL SECURITY;

            -- RLS policies for profiles
            CREATE POLICY "Public profiles are viewable by everyone."
              ON public.profiles FOR SELECT USING (true);

            CREATE POLICY "Users can insert their own profile."
              ON public.profiles FOR INSERT WITH CHECK (auth.uid() = id);

            CREATE POLICY "Users can update their own profile."
              ON public.profiles FOR UPDATE USING (auth.uid() = id);

            -- Function to copy user metadata to profiles table on new user creation
            CREATE OR REPLACE FUNCTION public.handle_new_user()
            RETURNS TRIGGER AS $$
            BEGIN
              INSERT INTO public.profiles (id, username, avatar_url, full_name)
              VALUES (
                NEW.id,
                NEW.raw_user_meta_data->>'user_name', -- or preferred_username from GitHub
                NEW.raw_user_meta_data->>'avatar_url',
                NEW.raw_user_meta_data->>'full_name'
              );
              RETURN NEW;
            END;
            $$ LANGUAGE plpgsql SECURITY DEFINER;

            -- Trigger to call the function on new user sign up
            CREATE TRIGGER on_auth_user_created
              AFTER INSERT ON auth.users
              FOR EACH ROW EXECUTE FUNCTION public.handle_new_user();
            ```
*   **Definition of Done:** GitHub OAuth provider is enabled and configured in the local Supabase instance using credentials from a dedicated GitHub OAuth App. The (optional) `profiles` table and associated RLS/trigger are set up.

### Task 7: Implement Frontend Login Flow with Local Supabase

*   **Action:**
    1.  Navigate to `src/`.
    2.  Install Supabase JS client: `npm install @supabase/supabase-js`.
    3.  Create environment variables for Supabase client in `src/.env.local` (this file should be in `.gitignore`):
        ```
        VITE_SUPABASE_URL=http://localhost:54321 # Your local Supabase API URL
        VITE_SUPABASE_ANON_KEY=<your_local_anon_key>
        ```
        (Get these values from `supabase status` or the output of `supabase start`.)
    4.  Create a Supabase client instance (e.g., `src/lib/supabaseClient.js` or `src/supabaseClient.js`):
        ```javascript
        // src/lib/supabaseClient.js
        import { createClient } from '@supabase/supabase-js'

        const supabaseUrl = import.meta.env.VITE_SUPABASE_URL
        const supabaseAnonKey = import.meta.env.VITE_SUPABASE_ANON_KEY

        if (!supabaseUrl || !supabaseAnonKey) {
          throw new Error("Supabase URL or Anon Key is missing. Check .env.local file.");
        }

        export const supabase = createClient(supabaseUrl, supabaseAnonKey)
        ```
    5.  Implement login UI and logic in `src/App.jsx` (or a dedicated auth component):
        ```jsx
        // src/App.jsx (Simplified example)
        import { useState, useEffect } from 'react'
        import { supabase } from './lib/supabaseClient' // Adjust path if needed

        function App() {
          const [session, setSession] = useState(null)
          const [profile, setProfile] = useState(null)

          useEffect(() => {
            // Check initial session
            supabase.auth.getSession().then(({ data: { session } }) => {
              setSession(session)
              if (session) fetchProfile(session.user.id)
            })

            // Listen for auth state changes
            const { data: { subscription } } = supabase.auth.onAuthStateChange((_event, session) => {
              setSession(session)
              if (session) {
                fetchProfile(session.user.id)
              } else {
                setProfile(null)
              }
            })
            return () => subscription.unsubscribe()
          }, [])

          async function fetchProfile(userId) {
            // Only if you created the profiles table
            const { data, error } = await supabase
              .from('profiles')
              .select('username, avatar_url')
              .eq('id', userId)
              .single()
            if (error) console.error('Error fetching profile:', error)
            else setProfile(data)
          }

          async function handleGitHubLogin() {
            const { error } = await supabase.auth.signInWithOAuth({
              provider: 'github',
            })
            if (error) console.error('Error logging in with GitHub:', error)
          }

          async function handleLogout() {
            const { error } = await supabase.auth.signOut()
            if (error) console.error('Error logging out:', error)
          }

          if (!session) {
            return (
              <div className="p-4">
                <button onClick={handleGitHubLogin} className="px-4 py-2 font-bold text-white bg-gray-800 rounded hover:bg-gray-700">
                  Login with GitHub
                </button>
              </div>
            )
          }

          return (
            <div className="p-4">
              <h1 className="text-xl">Logged in as: {session.user.email}</h1>
              {profile && (
                <div>
                  <p>Username: {profile.username}</p>
                  {profile.avatar_url && <img src={profile.avatar_url} alt="User avatar" className="w-16 h-16 rounded-full"/>}
                </div>
              )}
              <button onClick={handleLogout} className="px-4 py-2 mt-4 font-bold text-white bg-red-600 rounded hover:bg-red-500">
                Logout
              </button>
              {/* Placeholder for calling backend */}
            </div>
          )
        }
        export default App
        ```
    6.  Test the flow: Run frontend (`npm run dev`). Click "Login with GitHub". Authorize on GitHub. Verify redirection back to the app and display of user information. Test logout.
*   **Definition of Done:** User can successfully log in via GitHub using the local Supabase instance. User email (and profile info if `profiles` table is used) is displayed on the frontend. Logout works.

### Task 8: Implement Backend Auth Verification with Local Supabase

*   **Action:**
    1.  Navigate to `backend/`.
    2.  Create `.env` file (ignored by git) and `backend/.env.example`:
        ```
        # backend/.env
        LOCAL_SUPABASE_URL=http://localhost:54321
        LOCAL_SUPABASE_ANON_KEY=<your_local_anon_key>
        LOCAL_SUPABASE_SERVICE_ROLE_KEY=<your_local_service_role_key> # If needed for admin tasks
        ```
    3.  Implement JWT authentication dependency in `backend/app/core/dependencies.py`:
        ```python
        # backend/app/core/dependencies.py
        from fastapi import Depends, HTTPException, status
        from fastapi.security import OAuth2PasswordBearer
        from supabase import create_client, Client
        from supabase.lib.client_options import UserResponse # For type hinting
        import os
        from dotenv import load_dotenv

        load_dotenv() # Load .env file

        SUPABASE_URL = os.getenv("LOCAL_SUPABASE_URL")
        SUPABASE_KEY = os.getenv("LOCAL_SUPABASE_ANON_KEY") # Use anon key for get_user

        if not SUPABASE_URL or not SUPABASE_KEY:
            raise RuntimeError("Supabase URL or Key not found in environment variables.")

        supabase_client: Client = create_client(SUPABASE_URL, SUPABASE_KEY)
        oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token") # Placeholder, not used for direct auth

        async def get_current_user(token: str = Depends(oauth2_scheme)) -> UserResponse:
            try:
                user_response = supabase_client.auth.get_user(jwt=token)
                if not user_response or not user_response.user:
                    raise HTTPException(
                        status_code=status.HTTP_401_UNAUTHORIZED,
                        detail="Invalid authentication credentials",
                        headers={"WWW-Authenticate": "Bearer"},
                    )
                return user_response.user
            except Exception as e: # Catch specific Supabase/GoTrue errors if possible
                print(f"Auth error: {e}") # For debugging
                raise HTTPException(
                    status_code=status.HTTP_401_UNAUTHORIZED,
                    detail=f"Could not validate credentials: {e}",
                    headers={"WWW-Authenticate": "Bearer"},
                )
        ```
    4.  Create a protected route in `backend/app/routers/users.py` (new file):
        ```python
        # backend/app/routers/users.py
        from fastapi import APIRouter, Depends
        from app.core.dependencies import get_current_user # Adjust import path
        from supabase.lib.client_options import User # For type hinting

        router = APIRouter(prefix="/api/users", tags=["Users"])

        @router.get("/me", response_model_exclude_none=True) # response_model can be Pydantic model of User
        async def read_users_me(current_user: User = Depends(get_current_user)):
            return current_user
        ```
    5.  Include the new router in `backend/app/main.py`:
        ```python
        # backend/app/main.py
        # ... (other imports)
        from app.routers import users as users_router # Add this
        # ... (app initialization and CORS)
        app.include_router(users_router.router) # Add this
        # ... (@app.get("/api/ping"))
        ```
    6.  **Frontend Call:** Modify `src/App.jsx` to call this protected backend endpoint after login:
        ```jsx
        // Inside App component, after session is set
        const [backendUser, setBackendUser] = useState(null);

        async function fetchBackendUser(accessToken) {
          try {
            const response = await fetch('http://localhost:8000/api/users/me', { // Adjust port if backend runs elsewhere
              headers: {
                'Authorization': `Bearer ${accessToken}`
              }
            });
            if (!response.ok) {
              throw new Error(`HTTP error! status: ${response.status}`);
            }
            const data = await response.json();
            setBackendUser(data);
            console.log("Data from backend:", data);
          } catch (error) {
            console.error("Error fetching data from backend:", error);
            setBackendUser({ error: error.message });
          }
        }

        // Call this when session is available
        // useEffect(() => { ... if (session) fetchBackendUser(session.access_token); ... }, [session]);

        // Display backendUser info
        // {backendUser && <pre>Backend Response: {JSON.stringify(backendUser, null, 2)}</pre>}
        ```
        Ensure to call `fetchBackendUser(session.access_token)` when `session` is available.
    7.  Test: Log in on frontend. Verify the frontend calls the backend `/api/users/me` endpoint with the JWT, and the backend successfully validates it and returns user data.
*   **Definition of Done:** Backend has a protected `/api/users/me` endpoint. Frontend successfully calls this endpoint with the JWT obtained from local Supabase login, and backend validates the token correctly.

### Task 9: Basic CI/CD Pipeline Setup (GitHub Actions)

*   **Action:**
    1.  Create workflow file: `.github/workflows/ci.yml`.
    2.  Implement CI jobs:
        ```yaml
        # .github/workflows/ci.yml
        name: Jules CI

        on:
          push:
            branches: [ main, develop ] # Adjust as per your branching strategy
          pull_request:
            branches: [ main, develop ]

        jobs:
          frontend-checks:
            runs-on: ubuntu-latest
            defaults:
              run:
                working-directory: ./src # Assuming frontend is in src/
            steps:
              - uses: actions/checkout@v4
              - name: Set up Node.js
                uses: actions/setup-node@v4
                with:
                  node-version: '18' # Or your project's Node version
                  cache: 'npm'
                  cache-dependency-path: src/package-lock.json
              - name: Install dependencies
                run: npm ci
              - name: Lint
                run: npm run lint # Assuming you have a lint script in package.json
              - name: Build
                run: npm run build

          backend-checks:
            runs-on: ubuntu-latest
            defaults:
              run:
                working-directory: ./backend
            steps:
              - uses: actions/checkout@v4
              - name: Set up Python
                uses: actions/setup-python@v5
                with:
                  python-version: '3.9' # Or your project's Python version
              - name: Install dependencies
                run: |
                  python -m pip install --upgrade pip
                  pip install -r requirements.txt
              - name: Lint with Ruff (example)
                run: |
                  pip install ruff
                  ruff check .
              # - name: Lint with Flake8 (alternative)
              #  run: |
              #    pip install flake8
              #    flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics
              #  # exit-zero treats all errors as warnings. The GitHub editor is 127 chars wide
              #    flake8 . --count --exit-zero --max-complexity=10 --max-line-length=127 --statistics
              - name: Test with Pytest
                run: pytest
        ```
        (Ensure you have `lint` scripts in `src/package.json` and `pytest` configured for backend.)
    3.  Commit and push `ci.yml`. Verify the action runs on GitHub for a test push/PR.
*   **Definition of Done:** GitHub Actions workflow is created and successfully runs basic linting, building (for frontend), and testing (for backend) on pushes/PRs.

### Task 10: Documentation, Review, and Phase 1 Wrap-up

*   **Action:**
    1.  **Documentation:**
        *   Ensure `backend/.env.example` and `src/.env.local.example` (or similar naming) exist and list all required environment variables.
        *   Update root `README.md` with clear instructions on how to set up the local development environment, run the frontend, run the backend, and start local Supabase services.
        *   Ensure this `phase1.md` document is finalized and accurate.
    2.  **Code Review:** Conduct a team review of all code committed during Phase 1 (frontend, backend, CI scripts).
    3.  **Final End-to-End Test:** Perform a full manual test of the local login flow:
        *   `npx supabase start`
        *   Start backend server.
        *   Start frontend server.
        *   Log in via GitHub on the frontend.
        *   Verify user info display on frontend (from Supabase client and from backend API call).
        *   Log out.
    4.  Commit all final changes and documentation.
*   **Definition of Done:** All Phase 1 code is reviewed. Setup and run instructions are clearly documented. `.env.example` files are present. This `phase1.md` is finalized. A final end-to-end test of the local authentication flow is successful.

## 5. Definition of Done for Phase 1

*   All tasks in Section 4 are completed, verified, and committed to the `bar181/jules` repository.
*   Developers can clone the repository, follow documented steps to set up local environments (including local Supabase), and run both frontend and backend applications.
*   A user can successfully authenticate via GitHub using the local Supabase instance, see their basic user information displayed on the frontend (sourced from both Supabase client-side session and a call to the protected backend API).
*   The basic CI pipeline (GitHub Actions) automatically runs linters, build checks (frontend), and tests (backend) on relevant pushes/PRs.
*   This `phase1.md` document is complete, accurate, and reflects the work done.

## 6. Potential Risks & Mitigations

*   **Risk:** Docker/Supabase CLI setup issues on different OS.
    *   **Mitigation:** Provide clear, tested instructions. Allocate time for troubleshooting. Use official Docker/Supabase documentation.
*   **Risk:** GitHub OAuth callback URL misconfiguration for local Supabase.
    *   **Mitigation:** Double-check the auth callback URL provided by `supabase start` (usually `http://localhost:54321/auth/v1/callback`) and ensure it matches exactly in the GitHub OAuth App settings.
*   **Risk:** CORS issues between frontend and backend.
    *   **Mitigation:** Ensure backend FastAPI CORS middleware correctly lists the frontend development server origin (e.g., `http://localhost:5173`).
*   **Risk:** JWT validation complexities or environment variable issues for backend.
    *   **Mitigation:** Use `supabase-py`'s `auth.get_user()` for robust validation. Ensure `.env` files are correctly loaded and variables are accessible. Print/log variables during startup for debugging.
*   **Risk:** CI pipeline flakiness or configuration errors.
    *   **Mitigation:** Start with simple CI steps. Test CI changes on a feature branch before merging.

## 7. Communication & Coordination

*   Regular (e.g., daily brief) stand-ups to discuss progress, blockers, and next steps.
*   Use GitHub Issues for tracking specific sub-tasks, bugs, or questions.
*   All code changes to be submitted via Pull Requests and reviewed by at least one other team member (if applicable).
*   A shared communication channel (e.g., Slack, Discord) for quick questions and updates.

---
