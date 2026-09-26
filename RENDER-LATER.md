# Kingwood Book Club

A responsive book-club website with a private organizer dashboard, persistent RSVPs, events, news, a searchable bookshelf, image uploads, calendar downloads, and Excel/CSV attendee exports.

**Start without paid hosting.** You can run the full app on your own computer, try the admin dashboard and RSVPs, and save the code in GitHub now. Connect the paid Render services whenever you are ready to launch for members.

**Eventual hosting:** GitHub Pages serves the public website; Render runs the Python backend and PostgreSQL database. Both use the same repository. Admin edits appear without republishing Pages, and secure RSVP/admin forms open on Render.

The included content is explicitly fictional demo content. No real member data, admin password, affiliate URL, club logo, or Instagram screenshot is included.

## What you can do now, and what can wait

| Part | Start now without paid hosting? | What works |
| --- | --- | --- |
| Full app on your computer | Yes | All public pages, admin login, book/news/event editing, RSVP testing, uploads, calendars, Excel/CSV exports |
| Local SQLite database | Yes | Saves your local records between restarts; no database account needed |
| Local image storage | Yes | Saves uploaded images in the project’s `uploads/` folder |
| GitHub repository | Yes, with GitHub Free | Stores your application code and setup files |
| GitHub Pages hosting | Available free for public repositories | Hosts the frontend, but this app still needs a reachable backend to load club content |
| Render backend, PostgreSQL, persistent image disk | Later | Keeps the complete app available online for members; the supplied Blueprint selects paid resources |
| Custom domain | Optional, later | The provided GitHub Pages and Render addresses are enough to launch |

GitHub confirms that Pages is included with GitHub Free for **public repositories**; private-repository Pages requires an eligible paid plan. [GitHub Pages availability](https://docs.github.com/en/pages/getting-started-with-github-pages)

**Your no-payment path:** complete Steps 1 and 2 below, then stop. No Render account, payment card, hosted database, or custom domain is needed for those steps. Local URLs work only on your computer; they are not public member links.

## Before you start

Download and unzip the project. The folder containing `app/`, `frontend/`, `requirements.txt`, and `render.yaml` is the project folder. On your Mac, open Terminal and type `cd ` (including the space), drag that folder into Terminal, then press Return. Run the commands below from that folder.

## Step 1 — Run the full app on your computer (no paid services)

Install Python 3.12 or newer from [python.org](https://www.python.org/downloads/) if needed. Python is free. Open a terminal in the project folder. You need an internet connection for the initial package download, but no hosting subscription.

### Create a virtual environment

macOS/Linux:

```bash
python3 -m venv .venv
source .venv/bin/activate
```

Windows PowerShell:

```powershell
py -m venv .venv
.\.venv\Scripts\Activate.ps1
```

Install packages and create a local configuration:

```bash
python -m pip install -r requirements-dev.txt
```

Copy `.env.example` to `.env` (use your file manager, or `cp .env.example .env` on macOS/Linux). Generate a secret:

```bash
python -c "import secrets; print(secrets.token_urlsafe(48))"
```

Paste it into `SECRET_KEY` in `.env`. Set `ADMIN_EMAIL` and `ADMIN_PASSWORD`, or leave them empty to be prompted by the command below. Keep the local URLs from the example. The SQLite database stays in `club.db` between restarts.

```bash
python -m app.manage init-db
python -m app.manage create-admin
python -m app.manage seed-demo
uvicorn app.main:app --host 127.0.0.1 --port 8000
```

Open [your local club website](http://127.0.0.1:8000). Sign in at [your local admin page](http://127.0.0.1:8000/admin/login) with the email/password you just set. Keep this terminal running.

Try changing the monthly book, posting news, creating an event, submitting a test RSVP, and exporting its attendance list. These features all work locally. The `seed-demo` command is a one-time setup step; skip it when restarting an existing preview. Restart the app later by activating the virtual environment and running the `uvicorn` command again.

### Optional: preview the GitHub Pages arrangement locally

To preview the **split GitHub Pages version**, open a second terminal, activate the same virtual environment, and run:

macOS/Linux:

```bash
BACKEND_URL=http://127.0.0.1:8000 python scripts/build_pages.py
python -m http.server 8080 --bind 127.0.0.1 --directory dist
```

Windows PowerShell:

```powershell
$env:BACKEND_URL='http://127.0.0.1:8000'
python scripts/build_pages.py
python -m http.server 8080 --bind 127.0.0.1 --directory dist
```

Open `http://127.0.0.1:8080`. Use `127.0.0.1` consistently: `localhost` is a different origin for CORS. Stop either local server with Ctrl+C.

### Where your local work is saved

Your database is `club.db`; uploaded images are in `uploads/`. Keep a private backup of both if you add content you want to preserve. They stay on your computer and are deliberately excluded from GitHub. The app stops being available when its local server stops, but those saved files remain.

Use fictional attendees while testing. Calendar downloads work locally, but their website links point back to your computer until you configure the live backend address.

## Step 2 — Save the project in GitHub (no paid hosting)

1. Create a repository with GitHub Free, or use your existing repository. Public or private is fine for storing the code; eventual free Pages hosting requires a public repository. Copy the **contents** of `kingwood-book-club` to the root of your repository, including `.github`, `.gitignore`, and `.env.example`. Finder may hide dotfiles; press Command–Shift–Period to show them.
2. The repository root should contain `app/`, `frontend/`, `scripts/`, `.github/`, `requirements.txt`, and `render.yaml`.
3. **Before uploading, pause Pages publishing:** rename `.github/workflows/pages.yml` to `.github/workflows/pages.yml.disabled`. This prevents the deployment workflow from trying to publish before a backend is available. Keep `tests.yml` as it is. If the repository is already uploaded, open **Actions → Publish GitHub Pages → ⋯ → Disable workflow** instead.
4. Commit and push to your `main` branch. If you use a different branch, change the branch in `.github/workflows/pages.yml`.
5. Do not upload a local `.env`, `club.db`, `uploads/`, `.venv/`, or `dist/`. The included `.gitignore` excludes them.
6. Leave `BACKEND_URL` unset and Pages publishing paused for now. Do not create a Render Blueprint yet. A Pages run made before this pause may fail because the backend address is missing; it does not mean the app is broken.

**You can stop here.** Your app works locally, and your code is saved in GitHub. Nothing in these steps creates a paid Render service. The existing test workflow can run within your GitHub account’s included Actions allowance; check your account’s usage settings before enabling extra paid usage.

### Can I publish the full website on Pages before adding Render?

The hosting itself can be free, but **this version needs an online backend to display its live content**. Publishing it without one will not produce a working club website. Keep using the local preview until you connect Render. A separate static “coming soon” page or read-only demo would need an additional change; it is not included here.

Do not set GitHub’s `BACKEND_URL` to `localhost` or `127.0.0.1`. Those addresses would refer to each visitor’s own computer, not yours.


## Step 3 — Later: connect the paid backend on Render

**Skip this whole step until you want paid hosting.**

The Blueprint creates a Python web service, a PostgreSQL database, and a persistent image disk. **These are paid Render resources.** Review the plans and pricing in Render before creating them. Nothing in this project has purchased or deployed a service for you.

1. Sign in to Render and connect the GitHub repository.
2. Choose **New → Blueprint** and select that repository. Render reads `render.yaml`.
3. Enter these environment values when prompted:

   | Variable | What to enter |
   | --- | --- |
   | `APP_URL` | The backend's HTTPS address, such as `https://YOUR-SERVICE.onrender.com`, with no trailing slash |
   | `PUBLIC_SITE_URL` | Your Pages address, including the repository path, such as `https://YOUR-NAME.github.io/YOUR-REPO/` |
   | `PAGES_ORIGINS` | The Pages origin only: `https://YOUR-NAME.github.io` (no repository path). For a custom domain, use its HTTPS origin. Separate multiple origins with commas. |
   | `ADMIN_EMAIL` | Your administrator email |
   | `ADMIN_PASSWORD` | A unique password of at least 12 characters |

4. The Blueprint generates `SECRET_KEY`, connects `DATABASE_URL` to PostgreSQL, and sets `UPLOAD_DIR=/var/data/uploads`. Leave those values in place.
5. If Render assigns a different service URL than expected, correct `APP_URL` in the service's **Environment** section and redeploy. `APP_URL` controls trusted host validation and calendar links, so it must match the actual backend hostname.
6. After deployment, open the web service's **Shell** and run:

   ```bash
   python -m app.manage create-admin
   ```

   This reads your administrator environment values and stores an Argon2 password hash in PostgreSQL. There is no public account-registration page and no default password.
7. Remove `ADMIN_PASSWORD` from Render's environment after creating the account. It is needed only when running `create-admin` to create/reset an account. Removing it does not delete the account.
8. Optionally load the labeled preview content:

   ```bash
   python -m app.manage seed-demo
   ```

9. Visit `https://YOUR-SERVICE.onrender.com/admin/login` and sign in. The service health endpoint is `/health`.

Tables initialize automatically on startup. Do not load the demo seed into a live club database; it is designed for a new database and refuses when books already exist.

### PostgreSQL and image persistence

Use the Render database's **internal** connection string as `DATABASE_URL`. The Blueprint does this automatically. For a separately created database, copy its internal URL into the web service's Environment settings. `postgres://`, `postgresql://`, and `postgresql+psycopg://` URLs are supported.

Uploaded images are re-encoded as JPEG, limited to 5 MB input and 1,800-pixel dimensions, and saved on the persistent disk. **Do not remove the disk or change `UPLOAD_DIR` to an ephemeral folder** or uploads will be lost on redeploy. This version runs one worker/instance and is designed for a small club. Moving images to S3, Cloudinary, or another object store should precede horizontal scaling. Back up the database and image disk; deleted records cannot be recovered from the app itself. Unreferenced upload files are retained intentionally and may need occasional maintenance.

### Bring your local content online later

A new Render PostgreSQL database starts empty. Pushing code to GitHub does **not** transfer your local `club.db` or uploaded images.

For a small amount of content, the easiest path is to sign in to the new online admin dashboard, add your books/events/news, copy your About text, and upload your images again. If you build a larger collection locally, keep a private backup and arrange a SQLite-to-PostgreSQL data migration plus image transfer before launch. An automated migration tool is not included. Do not upload the SQLite file as your production PostgreSQL database.

## Step 4 — Later: connect GitHub Pages to the live backend

**Do this after the backend works online.** GitHub Pages can still use its free public-repository hosting; the paid portion is the separate Render setup above.

1. In your GitHub repository, open **Settings → Secrets and variables → Actions → Variables**.
2. Add a **repository variable** named `BACKEND_URL`. Set it to the exact backend origin, such as `https://YOUR-SERVICE.onrender.com`. This URL is public configuration, not a secret.
3. Go to **Settings → Pages** and choose **GitHub Actions** as the build/deployment source.
4. Restore `.github/workflows/pages.yml.disabled` to `.github/workflows/pages.yml`, then commit and push. If you used the Actions menu to disable it, enable it there. Open **Actions → Publish GitHub Pages → Run workflow** if it does not run automatically. Subsequent pushes to `main` publish automatically.
5. Open the address shown by the deployment. Test Home → Event → Continue to RSVP → Confirmation → Add to Calendar.
6. Test the subtle **Admin** footer link. It opens the Render login.

The workflow builds only `frontend/` and the public CSS/JavaScript/icons into a Pages artifact. It does **not** publish Python source, a database, uploads, environment files, admin credentials, or RSVP records.

Repository subpaths work because assets are relative and public navigation uses hash routes, for example `https://YOUR-NAME.github.io/YOUR-REPO/#/events/1`. Refreshing a detail page does not produce a GitHub Pages 404. A custom domain also works; update `PUBLIC_SITE_URL` and `PAGES_ORIGINS` in Render when switching domains.

If the site shows “A little pause,” first open the backend URL directly. Check that Render is running and `PAGES_ORIGINS` matches the **origin** of the Pages site exactly. The browser's network panel will distinguish a CORS configuration error from a backend outage.

Official setup references: [GitHub Pages custom workflows](https://docs.github.com/en/pages/getting-started-with-github-pages/using-custom-workflows-with-github-pages), [Render Blueprints](https://render.com/docs/infrastructure-as-code), [Render persistent disks](https://render.com/docs/disks).

## Managing the club

- **Monthly selection:** Dashboard → Change Book of the Month → add the new book → select “Make this the Book of the Month” and “Archive current book to Past Reads.” If you leave archive unchecked, the old record is retained for historical event links but hidden from the public bookshelf. Edit the new event and select that book to associate its meeting. Editing a book in place changes that record; adding a new one preserves the previous book.
- **Past Reads:** Manage Past Reads → add, edit, or remove books, ratings (0–5), discussion notes, discussion questions, cover, and optional affiliate URL.
- **Events:** Create or edit the date, Central Time start/end, venue, address, capacity, deadline, guest permissions, public attendee count, and associated book. “Closed” stops new and updated RSVPs. Duplicated events start closed so they can be reviewed before opening.
- **RSVPs:** Dashboard → select the event under Upcoming Events, or Manage Events → View RSVPs / Export. Excel and CSV include guest details, dietary restrictions, notes, and submission date. Capacity counts confirmed readers plus guests; Maybe/Declined do not consume seats.
- **RSVP updates:** A repeat submission in the same browser updates the existing event/email response. Another browser needs the private update link shown on the confirmation page. Save that link; do not share it. This protects attendees from someone changing a response just by knowing an email address. There is no email delivery or automated link recovery in this version.
- **News:** Set a publish date/time to schedule a post. Past dates publish immediately. Categories and associated-event links are supported.
- **About:** Edit all five About sections from the dashboard.
- **Images:** Use a HTTPS image URL or upload a PNG/JPEG/WebP file. Uploaded files take precedence over the URL. No SVG upload is accepted.
- **Affiliate links:** Enter your own HTTPS Amazon affiliate URL. The button stays hidden when no URL is set, and a disclosure appears when a link is present. No affiliate URLs are generated.
- **Going live:** Replace all fictional demo records and About copy, upload real book covers, confirm all dates/venues, then run `python -m app.manage remove-demo-banner` in Render Shell. The provided wordmark is a temporary text treatment, not the club's official logo.

## Environment variables

| Variable | Purpose |
| --- | --- |
| `DATABASE_URL` | SQLite locally; PostgreSQL on Render |
| `SECRET_KEY` | Long random signing key; required in production; changing it signs everyone out |
| `ADMIN_EMAIL`, `ADMIN_PASSWORD` | Used only by the create/reset administrator command |
| `APP_URL` | Backend origin used for trusted host validation and calendar/detail links |
| `PUBLIC_SITE_URL` | Full GitHub Pages URL used for return-to-site links |
| `PAGES_ORIGINS` | Allowed browser origins for the read-only public API; comma-separated |
| `ENVIRONMENT` | `production` enables secure cookies and HTTPS requirements |
| `UPLOAD_DIR` | Local image directory; must point to persistent storage in production |
| `BACKEND_URL` | GitHub repository variable used only by the static Pages build |
| `PORT` | Supplied automatically by Render |

Change backend variables in Render's Environment settings and redeploy. Change `BACKEND_URL` in GitHub repository variables and rerun the Pages workflow. Never put passwords, database URLs, or `SECRET_KEY` in GitHub **variables**, frontend configuration, or browser code.

## Architecture and files

```text
app/
  main.py             FastAPI routes, validation, security, calendar/export logic
  models.py           SQLAlchemy relational models
  manage.py           Database/admin/demo commands
  templates/          Jinja public pages and private dashboard
  static/             Shared custom CSS, JavaScript, favicon
frontend/
  index.html          GitHub Pages entry point
  pages.js            Read-only backend content loading and hash navigation
scripts/build_pages.py  Builds dist/ with only public assets/configuration
.github/workflows/    Pages deployment and automated tests
render.yaml           Render service, Postgres, persistent image disk
SCHEMA.md             Database relationships and route inventory
requirements*.txt     Runtime and test dependencies
.env.example          Non-secret environment template
```

The frontend requests a whitelisted public HTML endpoint (`/api/public/...`) and uses the same templates as the backend. It rewrites internal public links to hash routes and image/calendar URLs to backend URLs. No credentials are sent by the Pages fetch. Admin and RSVP writes remain ordinary same-origin server-rendered forms on Render with CSRF protection. This avoids duplicating templates and avoids third-party-cookie dependence.

## Tests

```bash
python -m pytest -q
```

The automated suite uses a separate temporary SQLite database. It covers the public RSVP/update/calendar flow; admin login, new book, archive, news/event creation, guest list and Excel/CSV exports; authorization, CSRF, CORS and output escaping; capacity and scheduling; image validation; deletion; and Pages build output. Tests build `dist/` with a test backend address; rerun your local Pages build afterward if previewing.

The same tests run in GitHub Actions on pushes and pull requests. Before deploying changes to an existing live database, back it up and review schema changes. `create_all` creates missing tables; it does **not** migrate existing table columns. Future schema changes need a deliberate migration (for example Alembic).

## Practical limits

- The project is ready to configure in your repository; it is not already deployed to a GitHub/Render account.
- The Pages public site needs JavaScript and an available backend. The complete server-rendered site remains available directly on Render.
- Hash-route Pages links cannot provide event-specific social previews to most crawlers. Share the corresponding Render `/events/ID` link for server-rendered title/description and the event image when supplied. The real logo was not supplied, so no logo sharing image is fabricated.
- No outbound email, password-reset email, analytics, or Instagram API is configured. Password reset is the `create-admin` command; a lost RSVP update link requires organizer assistance.
- Login throttling is in memory and resets on a service restart; the deployment uses one worker. A shared rate-limit store is appropriate before scaling.
- Events use America/Chicago, same-day start/end times. Scheduled publication is evaluated when pages are requested; no background scheduler is needed.
- PostgreSQL and the paid persistent disk must be provisioned in Render. Automated tests use SQLite; run a deployment smoke test against your actual PostgreSQL service before inviting members.
- Uploaded images are public content. RSVP names/emails/dietary information are accessible only to the organizer and the attendee's private update link. Keep the private links and admin credentials confidential.
