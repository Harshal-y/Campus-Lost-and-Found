# Campus Lost & Found

A static GitHub Pages frontend with a Java API backend and Supabase database/storage. The frontend can be published independently before the backend is connected.

## Project layout

```text
.
├── frontend/
│   ├── index.html          # Website UI
│   ├── config.js           # Public backend URL setting (no secrets)
│   └── .nojekyll
├── backend/
│   ├── LostFoundServer.java
│   ├── Dockerfile
│   ├── render.yaml
│   └── supabase.sql
├── .github/workflows/deploy-pages.yml
└── render.yaml             # Optional Render Blueprint
```

## 1. Deploy the frontend on GitHub Pages (do this now)

1. Create a GitHub repository and upload/push the contents of this project to the `main` branch.
2. Open **Repository → Settings → Pages**.
3. Under **Build and deployment → Source**, select **GitHub Actions**.
4. The workflow `.github/workflows/deploy-pages.yml` automatically publishes the `frontend/` folder whenever you push to `main`.
5. After the workflow succeeds, your site will be available at `https://YOUR-USERNAME.github.io/YOUR-REPOSITORY/` (or your configured custom domain).

The frontend intentionally starts in an unconnected state. It displays a setup message rather than making requests to a fake backend URL.

## 2. Connect the backend later

The Java backend is in `backend/` and is still deployable on Render.

1. Create a Supabase project.
2. Run `backend/supabase.sql` in the Supabase SQL Editor.
3. Deploy the repository to Render as a Docker web service. The root `render.yaml` uses `backend/` as its build root.
4. Set these Render environment variables:
   - `SUPABASE_URL`: your Supabase project URL.
   - `SUPABASE_KEY`: the Supabase **service_role** secret. Keep it only in Render's environment settings; never add it to GitHub or frontend files.
   - `ADMIN_KEY`: a long, unique password for the host suggestion inbox.
   - `CORS_ORIGIN`: your exact GitHub Pages origin, e.g. `https://YOUR-USERNAME.github.io`. Do not include the repository path or a trailing slash. For a custom domain, use that domain's origin instead.
5. Once Render gives you the backend URL, open `frontend/config.js` and set:

   ```js
   window.LF_CONFIG = {
     API_BASE: "https://YOUR-BACKEND.onrender.com"
   };
   ```

6. Commit and push the change. GitHub Actions republishes the frontend with the backend URL configured.

## 3. Run backend locally

Requires JDK 17 or newer.

From the repository root:

```bash
# Linux/macOS
export SUPABASE_URL="https://YOUR-PROJECT.supabase.co"
export SUPABASE_KEY="YOUR_SERVICE_ROLE_KEY"
export ADMIN_KEY="YOUR_LONG_RANDOM_ADMIN_PASSWORD"
export CORS_ORIGIN="http://localhost:8000"
cd backend
javac LostFoundServer.java
java LostFoundServer
```

On Windows PowerShell, use `$env:NAME="value"` for each environment variable before running `javac` and `java`.

## Important security notes

- Never put `SUPABASE_KEY`, `ADMIN_KEY`, or any service-role/secret key in `frontend/config.js`, HTML, JavaScript, or a public GitHub repository.
- `API_BASE` is public configuration; it is safe to expose the backend URL.
- The backend requires environment variables at startup. There is no insecure default admin password.
- Configure `CORS_ORIGIN` to the exact origin serving your frontend.
- The current application uses browser-generated visitor IDs for ownership. This is lightweight identification, not strong authentication; do not treat it as proof of identity for sensitive data.

## Existing application features

Found/lost item posts, item photos via Supabase Storage, claims, returned-item status, personal post management, and a host-only suggestion inbox.
"# Campus-Lost-and-Found" 
