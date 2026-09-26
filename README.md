# Kingwood Book Club — Free GitHub Pages version

This version publishes books, events, news, Past Reads, About, Instagram links, purchase links, and calendar downloads entirely on GitHub Pages. **No Render account, backend subscription, or hosted database is required.** Online RSVPs display “Coming soon.” There is no public admin login or online RSVP collection.

You keep editing in the private local app on your Mac. When you want your changes to appear online, export a public snapshot and upload the new `docs` folder to GitHub. The website keeps working when your Mac is off.

## Publish the first version

1. Open your `KWBC_test` repository on GitHub.
2. Choose **Add file → Upload files**.
3. Drag in the entire **`docs` folder** from this project or from the supplied Pages upload ZIP. Do not drag in `club.db`, `.env`, `.venv`, or the raw `uploads` folder.
4. Click **Commit changes**. The repository should now have a top-level `docs` folder containing `index.html`, `content.json`, `assets`, and `calendars`.
5. Open **Settings → Pages**.
6. Under **Build and deployment**, set **Source** to **Deploy from a branch**.
7. Choose **main** and **/docs**, then click **Save**.
8. Wait for GitHub to finish publishing. The Pages settings screen will show the website address. Open that address to check the site.

GitHub Pages supports free hosting for public repositories. Your repository is public; publishing this folder needs no Render billing account. The normal GitHub Pages usage limits still apply. See [GitHub Pages documentation](https://docs.github.com/en/pages/getting-started-with-github-pages).

**If the old “Publish GitHub Pages” workflow is enabled:** disable it under Actions before using branch publishing. The replacement project stores this optional workflow as `pages.yml.disabled` so it does not run accidentally. You do not need that workflow, `BACKEND_URL`, or any GitHub secrets for the branch-and-`/docs` method.

## Edit content on your Mac

Your existing local administrator login, database, and image uploads still work. Open your local site at `http://127.0.0.1:8001/admin/login` while the local server is running. Edit books, events, announcements, and About there.

For your current Mac folder, start the local app with:

```bash
cd "/Users/meganpillow/Desktop/kingwood-book-club 2"
source .venv/bin/activate
APP_URL=http://127.0.0.1:8001 python -m uvicorn app.main:app --host 127.0.0.1 --port 8001
```

Leave that Terminal window open. For another computer, use its actual project folder rather than the example path above.

## Publish later content changes

1. Save your edits in the local admin dashboard.
2. Open a **second Terminal window** so the website can keep running.
3. Run these commands one at a time:

```bash
cd "/Users/meganpillow/Desktop/kingwood-book-club 2"
```

```bash
source .venv/bin/activate
```

```bash
python scripts/export_static.py
```

4. The exporter refreshes the `docs` folder. It includes only public pages, referenced public images, and calendar downloads.
5. Upload that whole `docs` folder to the same place in GitHub and commit. Updated files replace the previous copies. GitHub republishes automatically.

**If you removed an image:** delete its old file from GitHub’s `docs/images` folder too. Browser uploads replace matching files but do not remove files missing from your new export. Using Git to synchronize the folder handles deletions more conveniently. Deleted public images may also remain in Git history or caches; upload only images intended to be public.

### Add your live website address to calendar downloads

Your `static-site.json` already has `https://meganapillow.github.io/KWBC_test`, so ordinary exports include that address. If you change repositories or domains, update that file or override it with:

```bash
python scripts/export_static.py --site-url "https://YOUR-USERNAME.github.io/KWBC_test"
```

Replace `YOUR-USERNAME` with your real GitHub username (`meganapillow` for this repository). This adds the correct event-page link to Google/Outlook links and downloaded calendar files. Without this option, dates, times, venue, and descriptions still work; the optional website URL is omitted. Never use the local `127.0.0.1` address for public calendar links.

For future exports, you can save the address in your private `.env` file as `STATIC_SITE_URL=https://YOUR-USERNAME.github.io/KWBC_test`. Keep `.env` off GitHub.

## Preview the exported website before uploading

From your activated project folder, run:

```bash
python -m http.server 8082 --bind 127.0.0.1 --directory docs
```

Open `http://127.0.0.1:8082`. This serves only static files. It does not use the Python app’s backend or database. Stop this preview with Control–C. Avoid double-clicking `index.html`; browsers normally block loading its content file through a `file://` address.

## What is available and what is postponed

| Available on GitHub Pages | Postponed |
| --- | --- |
| Current book, cover, description, supplied purchase link/disclosure | Online RSVP submissions and updates |
| Event details, calendar/list views | Online admin login and editing |
| Google Calendar, Apple/Outlook, `.ics` downloads | Shared attendee lists and exports |
| Published news and category filters | Automatic synchronization from your local database |
| Past Reads, search and year/genre filters | Server-side scheduled publishing |
| About and Instagram links | |

Content is a snapshot at export time. Scheduled announcements appear only after their publication time **and** a new export/upload. New monthly books, revised events, deleted items, and archive changes also need a fresh export. Review date-sensitive content before publishing. RSVP counts are not included in the public export.

Public pages use links such as `/#/events/1`, which work on GitHub repository subpaths and refresh without a 404. Search/filter state stays in the link. The static site needs JavaScript; most social crawlers show the general website metadata rather than event-specific previews.

The supplied content may still be labeled demo. Replace fictional books, venues, and announcements before treating the site as official. If you no longer need the demo banner, run `python -m app.manage remove-demo-banner` locally after replacing all demo content, then export again. The wordmark remains a temporary text treatment until an official logo is supplied.

## What stays private

The exporter never copies the database, admin accounts, password hashes, sessions, RSVP records, email addresses from RSVPs, guest details, or unpublished announcements. It only includes uploaded files referenced by public records. Public book/event/news/About text is deliberately published, so do not put private information in those fields.

- `club.db`: local database — keep private and back it up.
- `uploads/`: local image originals — keep a private backup; only selected public images go into `docs/images/`.
- `.env`: local settings — keep private.
- `docs/`: generated public website — this is the folder to upload.

Keeping `.gitignore` in GitHub is correct. However, GitHub’s browser upload is not a substitute for checking which files you select: upload `docs`, not the private files above.

## New local installation (only if needed)

Existing users do not need to recreate their administrator account or database. For a new installation, use Python 3.12+, then:

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements-dev.txt
cp -n .env.example .env
python -m app.manage init-db
python -m app.manage create-admin
python -m app.manage seed-demo
```

Choose an administrator password of at least 12 characters. Generate a random local `SECRET_KEY` with `python -c "import secrets; print(secrets.token_urlsafe(48))"` and place it in `.env`. Then start the local server as described above. Demo seeding is only for an empty database.

## Tests and future hosting

Run `python -m pytest -q` for the original application flows plus public-export privacy checks. `python scripts/build_pages.py` optionally packages an existing `docs` export into `dist`; it needs no backend address or database.

The original backend is preserved for possible future use. `RENDER-LATER.md` contains the previous hosting guide as an archive; its backend-dependent frontend deployment instructions describe the older version and should not be followed for this static release. Restoring online RSVP/admin functionality will require reconnecting the frontend and deploying the backend deliberately. Do not activate Render now for this free static site.
