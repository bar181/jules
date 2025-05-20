# Phase 1 Implementation Plan for Jules Coding Assistant

Phase 1 sets up the local development environment, initializes the frontend (Vite + React + Tailwind) and backend (FastAPI) codebases, configures Supabase (including GitHub OAuth), and implements a basic login flow for verification. Each step below is sequential; complete one before moving to the next.

## Task 1: Setup Development Environment and Version Control

* **Functional Requirements**: Install required tools on the local machine: [Node.js](https://nodejs.org/) (for frontend), [Python 3.8+](https://www.python.org/) (for backend), the [Supabase CLI](https://supabase.com/docs/guides/cli) (for local Supabase), Docker (for running Supabase locally), and Git. Initialize a Git repository at the project root.
* **Setup Commands/Pseudocode**:

  ```bash
  # Install Node.js (ensure v16+), Python, Docker, and Git via system package manager or installers.
  node -v   # verify Node installation
  python3 -V  # verify Python installation
  git --version
  docker --version
  # Install Supabase CLI (after Node is set up):
  npm install -g supabase
  # Initialize Git repository:
  git init
  echo "node_modules/\n__pycache__/\n.env" >> .gitignore
  git add .gitignore
  git commit -m "Initial project setup"
  ```
* **Folder Structure Scaffolding**: Create separate subfolders for frontend and backend:

  ```
  project-root/
  ├── frontend/        # Vite + React + Tailwind project
  ├── backend/         # FastAPI project
  └── supabase/        # Supabase configuration (created by CLI)
  ```
* **TDD / Key Tests**:

  * Verify that `node` and `python3` commands work.
  * Verify Docker is running (`docker ps`).
  * After `supabase init`, check that a `.supabase` folder (with `config.toml`) appears.
* **Definition of Done**: All tools are installed and versions verified, Git repo initialized with a sensible `.gitignore`, and a `.supabase` directory is created (ready for configuration).

## Task 2: Initialize Frontend (Vite + React + TailwindCSS)

* **Functional Requirements**: Create a new Vite project with React, install and configure TailwindCSS, and verify the development server runs correctly with a sample styled component.
* **Setup Snippets**:

  ```bash
  cd project-root/frontend
  npm create vite@latest .       # Select "React" (JavaScript)
  npm install                    # Install dependencies
  npm install -D tailwindcss postcss autoprefixer
  npx tailwindcss init -p        # Generates tailwind.config.js and postcss.config.js
  ```

  In `tailwind.config.js`, ensure content paths include `src/**/*.{js,jsx}` and `index.html`. In `src/index.css`, add:

  ```css
  @tailwind base;
  @tailwind components;
  @tailwind utilities;
  ```
* **Folder/File Scaffolding**:

  ```
  frontend/
  ├── index.html
  ├── package.json
  ├── tailwind.config.js
  ├── postcss.config.js
  ├── src/
  │   ├── main.jsx
  │   ├── index.css
  │   └── App.jsx
  └── public/
  ```

  Example `tailwind.config.js` snippet:

  ```js
  /** @type {import('tailwindcss').Config} */
  export default {
    content: ["./index.html", "./src/**/*.{js,jsx,ts,tsx}"],
    theme: { extend: {} },
    plugins: [],
  };
  ```
* **TDD / Key Tests**:

  * Run `npm run dev` and open the app in the browser; ensure the default Vite+React page loads.
  * Modify `App.jsx` to include a Tailwind class (e.g. `<h1 className="text-3xl font-bold">Hello</h1>`). Verify that styling appears, confirming Tailwind is working.
* **Definition of Done**: Frontend project builds and serves (e.g., at [http://localhost:3000](http://localhost:3000) or [http://localhost:5173](http://localhost:5173)) without errors. Tailwind CSS classes apply correctly (you can see styled elements). The file structure matches the scaffold above.

## Task 3: Initialize Backend (FastAPI Project)

* **Functional Requirements**: Create a Python virtual environment, install FastAPI and Uvicorn, and set up a basic API with a health-check or “Hello world” endpoint.
* **Setup Snippets**:

  ```bash
  cd project-root/backend
  python3 -m venv venv
  source venv/bin/activate      # or `venv\Scripts\activate` on Windows
  pip install fastapi uvicorn
  pip install pytest httpx      # For testing
  ```

  Create `app/main.py`:

  ```python
  from fastapi import FastAPI

  app = FastAPI()

  @app.get("/ping")
  async def ping():
      return {"message": "pong"}
  ```

  Create `requirements.txt` from pip freeze.
* **Folder/File Scaffolding** (following FastAPI conventions):

  ```
  backend/
  ├── app/
  │   ├── __init__.py
  │   ├── main.py        # Creates FastAPI app and includes routes
  │   └── routers/
  │       └── auth.py    # (for future auth routes)
  ├── venv/             # Python virtual environment
  ├── requirements.txt
  └── .gitignore
  ```

  Example of using routers (per official docs):

  ```python
  # app/main.py
  from fastapi import FastAPI
  from app.routers import auth

  app = FastAPI()
  app.include_router(auth.router)
  ```
* **TDD / Key Tests**:

  * Create a test `tests/test_ping.py` using FastAPI’s `TestClient`:

    ```python
    from fastapi.testclient import TestClient
    from app.main import app

    client = TestClient(app)

    def test_ping():
        response = client.get("/ping")
        assert response.status_code == 200
        assert response.json() == {"message": "pong"}
    ```

    (As shown in FastAPI docs).
  * Run `pytest` to ensure the test passes.
* **Definition of Done**: Backend server starts with `uvicorn app.main:app --reload`. The `/ping` endpoint returns the expected JSON. All tests (e.g. `test_ping`) pass, and PEP8 is followed (use a linter/formatter if possible).

## Task 4: Initialize Supabase Local Environment

* **Functional Requirements**: Use the Supabase CLI to initialize a local Supabase project (Postgres + Auth) in the repository. This will allow local auth and database testing without deploying to the cloud.
* **Setup Snippets**:

  ```bash
  # From project root:
  npx supabase init       # Creates a supabase/ folder with config.toml
  npx supabase start      # Starts local Supabase (requires Docker)
  ```

  This will spin up local services (PostgreSQL, Auth, etc.). After starting, you can visit the local dashboard at `http://localhost:54323`.
* **Folder/File Scaffolding**:

  ```
  project-root/
  ├── supabase/                # Created by CLI
  │   ├── config.toml         # Supabase project config
  │   ├── migrations/         # (empty initially)
  │   └── functions/          # (if using edge functions later)
  └── ...
  ```

  The `supabase/config.toml` will contain project settings (no modifications needed for Phase 1).
* **TDD / Key Tests**:

  * Run `supabase start` and ensure no errors.
  * Check Docker: there should be Supabase containers (e.g. `supabase_auth_`, `supabase_db_`) running.
  * Access [http://localhost:54323](http://localhost:54323) and ensure the Supabase UI/dashboard is reachable.
* **Definition of Done**: Local Supabase services are running. The CLI reports success, and the Supabase dashboard can be accessed in the browser.

## Task 5: Configure Supabase Auth (GitHub OAuth) and Database Tables

* **Functional Requirements**: In the Supabase dashboard (or via SQL), enable GitHub as an OAuth provider, and create necessary tables (e.g. a `profiles` or `users` table) to store user metadata. Ensure Row Level Security (RLS) is enabled on user data tables and linked to `auth.users`.
* **Setup Snippets**:

  1. **Enable GitHub OAuth**:

     * In the Supabase dashboard, go to **Authentication > Settings**. Under "External OAuth Providers", enable GitHub.
     * On GitHub, register a new OAuth App (Settings > Developer settings > OAuth Apps). Set the callback URL to `${YOUR_SUPABASE_URL}/auth/v1/callback`. For local dev, `${YOUR_SUPABASE_URL}` will be `http://localhost:54323` (for CLI instance) or your project URL. Copy the Client ID and Secret.
     * In Supabase Auth settings, paste the GitHub Client ID and Secret and save.
  2. **Create User/Profiles Table**:
     Using the SQL editor in Supabase (or `supabase db remote commit`), create a table in the `public` schema to hold profile info. For example:

     ```sql
     CREATE TABLE public.profiles (
       id UUID PRIMARY KEY REFERENCES auth.users (id) ON DELETE CASCADE,
       username TEXT NOT NULL,
       created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
     );
     ALTER TABLE public.profiles ENABLE ROW LEVEL SECURITY;
     ```

     (Example pattern from Supabase docs).
     Insert a sample row for testing (replace with valid UUID from auth.users if needed):

     ```sql
     INSERT INTO public.profiles (id, username) 
     VALUES ('00000000-0000-0000-0000-000000000001', 'jules_dev');
     ```
* **Scaffolding and Sample Data**:

  | Table Name | Columns                                                                        | Sample Row Values                                                                       |
  | ---------- | ------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------- |
  | `profiles` | `id (UUID, PK, FK to auth.users)`, `username (text)`, `created_at (timestamp)` | `id`: `dfe50f45-...`, `username`: `"jules_dev"`, `created_at`: `"2025-05-20T13:45:00Z"` |
* **TDD / Key Tests**:

  * In Supabase SQL editor, run a `SELECT * FROM public.profiles;` query. The sample row should appear.
  * Verify RLS by trying a query as an unauthenticated user (should get zero rows).
  * In Supabase Auth > Users, after a GitHub login (next task), check that a new entry appears under Users.
* **Definition of Done**: GitHub OAuth is enabled with valid credentials in Supabase. The `profiles` table exists and RLS is active. You can query the table and see inserted data. The authentication provider lists GitHub as enabled (without errors).

## Task 6: Implement Frontend Login Flow with Supabase

* **Functional Requirements**: Integrate Supabase Auth into the React frontend. Provide a login button that triggers GitHub OAuth. After successful sign-in, display the logged-in user's information (e.g. username or email) in the UI.
* **Setup Snippets / Pseudocode**:

  1. **Install Supabase JS**:

     ```bash
     cd project-root/frontend
     npm install @supabase/supabase-js
     ```
  2. **Initialize Client** (e.g. in `src/supabaseClient.js`):

     ```js
     import { createClient } from '@supabase/supabase-js';

     const supabaseUrl = import.meta.env.VITE_SUPABASE_URL;
     const supabaseKey = import.meta.env.VITE_SUPABASE_ANON_KEY;
     export const supabase = createClient(supabaseUrl, supabaseKey);
     ```

     Add `VITE_SUPABASE_URL` and `VITE_SUPABASE_ANON_KEY` to a `.env.local` file (these come from Supabase dashboard **Settings > API** or your local config).
  3. **Login Button & Handler**:

     ```jsx
     // In App.jsx or a Login component:
     import { supabase } from './supabaseClient';

     function handleLogin() {
       supabase.auth.signInWithOAuth({ provider: 'github' }); // triggers redirect:contentReference[oaicite:8]{index=8}
     }
     return <button onClick={handleLogin}>Sign in with GitHub</button>;
     ```
  4. **Handle Redirect**: Upon successful auth, Supabase redirects back to the app (ensure your Vite dev URL is allowed as a redirect). In `main.jsx`, subscribe to auth state:

     ```js
     supabase.auth.onAuthStateChange((_event, session) => {
       if (session?.user) {
         // Save user to state or localStorage
         console.log("Logged in user:", session.user);
       }
     });
     ```
* **Scaffolding**:

  ```
  frontend/
  ├── src/
  │   ├── supabaseClient.js
  │   ├── App.jsx       # Contains login button and conditional user display
  │   ├── index.css
  │   └── main.jsx
  ├── .env.local        # Contains SUPABASE_URL and ANON_KEY
  ```
* **TDD / Key Tests**:

  * Mock or stub Supabase in tests (optional). Instead, manually test: run `npm run dev`, click "Sign in with GitHub". You should be redirected to GitHub, authorize, and then return to the app.
  * After redirect, `supabase.auth.getSession()` (or onAuthStateChange) should yield a session object with a `user` property (containing `id`, `email`, etc.). Print this in the console or UI to verify.
  * The login button should be visible when not signed in, and hidden or replaced with user info after signing in.
* **Definition of Done**: Clicking the login button launches the OAuth flow. After granting access on GitHub, the app returns to the frontend with a valid Supabase session. The UI shows the user’s information (e.g. GitHub username/email). No frontend errors occur during sign-in.

## Task 7: Implement Backend Auth Verification

* **Functional Requirements**: Add a protected backend route (e.g. `/user`) that requires a valid Supabase JWT and returns the current user’s profile. This verifies that the backend can parse and trust the auth token.
* **Setup Snippets / Pseudocode**:

  1. **Install Dependencies**: (if not already)

     ```bash
     cd project-root/backend
     pip install python-jose
     ```
  2. **JWT Verification Setup**: Create a dependency using FastAPI’s security utilities:

     ```python
     # app/routers/auth.py
     from fastapi import APIRouter, Depends, HTTPException
     from fastapi.security import HTTPBearer, HTTPAuthorizationCredentials
     import os, requests, jwt

     router = APIRouter(tags=["auth"])

     class JWTBearer(HTTPBearer):
         async def __call__(self, req):
             credentials: HTTPAuthorizationCredentials = await super().__call__(req)
             token = credentials.credentials
             try:
                 # Fetch Supabase public keys or use known HS256 secret from settings
                 SUPABASE_JWT_SECRET = os.getenv("SUPABASE_JWT_SECRET")  # from Supabase settings
                 decoded = jwt.decode(token, SUPABASE_JWT_SECRET, algorithms=["HS256"])
                 return decoded
             except Exception:
                 raise HTTPException(status_code=401, detail="Invalid token")

     @router.get("/user")
     async def get_current_user(payload=Depends(JWTBearer())):
         # Return decoded payload or fetch user info from DB
         return {"user_id": payload["sub"], "email": payload.get("email")}
     ```

     (In practice, you may use `supabase-py` and `supabase.auth.get_user(token)` to fetch user details, or decode the JWT with the Supabase secret.)
  3. **Include Router** in `app/main.py`:

     ```python
     from app.routers import auth
     app.include_router(auth.router, prefix="/api")
     ```
* **TDD / Key Tests**:

  * **Positive Test**: Obtain a valid JWT (from frontend after login). Use `TestClient` to call `/api/user` with header `Authorization: Bearer <JWT>`. Expect status 200 and JSON containing the correct user ID/email.
  * **Negative Test**: Call `/api/user` without a token or with an invalid token. Expect a 401 Unauthorized error.
  * Example (pytest):

    ```python
    def test_user_endpoint_requires_auth():
        client = TestClient(app)
        res_no_auth = client.get("/api/user")
        assert res_no_auth.status_code == 401
        res_bad_auth = client.get("/api/user", headers={"Authorization": "Bearer badtoken"})
        assert res_bad_auth.status_code == 401
        # For a valid token test, see note above.
    ```
* **Definition of Done**: The `/api/user` route returns the current user’s data when provided a valid Supabase JWT, and rejects requests without or with invalid tokens (HTTP 401). For example, with a valid token it returns JSON like `{"user_id": "...", "email": "..."}`. Key tests for both success and failure pass.

## Task 8: Review, Testing, and Definition of Done for Phase 1

* **Functional Requirements**: Ensure that all pieces work together locally. Document environment variables, and prepare for Phase 2 development.
* **Checklist**:

  * **Environment Variables**: Document variables needed, e.g. `VITE_SUPABASE_URL`, `VITE_SUPABASE_ANON_KEY`, and `SUPABASE_JWT_SECRET`. Ensure `.env` files are ignored in Git.
  * **Integration Test**: Manually test full flow: start backend and frontend dev servers, start Supabase (`supabase start`), register a GitHub OAuth app if not done, log in via frontend, then call the backend `/user` route with the returned JWT.
  * **Code Quality**: Run linters/formatters (ESLint, Prettier for JS; flake8/black for Python). Add a README.md summarizing the setup steps.
* **TDD / Key Tests**:

  * Frontend: Optionally write a basic unit test (e.g. using [Jest](https://jestjs.io/)) to verify that the login function calls `supabase.auth.signInWithOAuth`.
  * Backend: Ensure all pytest tests pass (`pytest`). Use the [FastAPI testing guide](https://fastapi.tiangolo.com/tutorial/testing/) for guidance.
* **Definition of Done**: The local development environment is fully operational. The login flow is verified end-to-end: a GitHub login yields a valid Supabase session, and the backend can validate it. All tests (frontend and backend) pass. The project structure matches the scaffolding above, and documentation is updated. Now Phase 2 (e.g. feature development) can proceed.

**Citations:** Steps and configurations above follow best practices and official docs (e.g. Vite+Tailwind setup, FastAPI project structure, Supabase GitHub OAuth setup, and Supabase auth flows). Each item is detailed for a solo developer to implement reliably.
