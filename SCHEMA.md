# Database and route reference

## Relational design

Every content record has an integer primary key plus created/updated timestamps. Dates and times use validated ISO strings; event and publication input is interpreted in `America/Chicago`. SQLAlchemy provides SQLite development and PostgreSQL production support.

| Table | Fields and relationships |
| --- | --- |
| `admins` | Unique email, Argon2 password hash, timestamps |
| `books` | Title, author, cover URL, description, genre, Amazon URL, month/year, current/archive flags, club rating, discussion notes/question, meeting date, timestamps |
| `events` | Title/type, date/start/end, venue/address, description/image, deadline, optional capacity, plus-one/count/closed flags, optional `book_id → books.id`, timestamps |
| `rsvps` | `event_id → events.id`, name, normalized email, yes/maybe/no status, dietary needs, guest flag/name/dietary needs, notes, unique secret update token, timestamps; unique `(event_id, email)` |
| `news` | Title/body/category, image, external URL/label, publish time, optional `event_id → events.id`, timestamps |
| `settings` | String key/value for editable About sections and demo banner |

Changing the current selection clears the old current flag and optionally keeps the old book as a past read. A partial unique index permits only one current book. Unarchived old records remain manageable but hidden from the public bookshelf. Events continue referencing their historical book. Removing a book clears associated event links; removing an event clears associated news links and deletes its RSVPs. Removal is explicit and protected by login and CSRF.

PostgreSQL event-row locking serializes concurrent capacity checks. The unique event/email constraint prevents duplicate RSVP records. Application queries use SQLAlchemy parameters. Templates escape stored text. Exports prefix spreadsheet formula-like values to prevent formula execution.

## Public website routes

GitHub Pages uses `/#/` and `/#/events`, `/#/events/ID`, `/#/news`, `/#/news/ID`, `/#/past-reads`, `/#/past-reads/ID`, `/#/about` after the repository base path. The backend exposes the same paths without `#`.

| Backend route | Purpose |
| --- | --- |
| `GET /` | Current book, meeting, events, newest three published announcements |
| `GET /events` | Upcoming/past events and calendar/list views |
| `GET /events/{id}` | Event details and secure RSVP form |
| `POST /events/{id}/rsvp` | Create/update validated RSVP |
| `GET /events/{id}/calendar.ics` | UTC iCalendar download with escaped/folded fields |
| `GET /confirmation/{token}` | Private response confirmation |
| `GET /rsvp/{token}` | Private response update form |
| `GET /news`, `/news/{id}` | Published news, category filter, article |
| `GET /past-reads`, `/past-reads/{id}` | Bookshelf with title/author/year/genre filtering; book detail |
| `GET /about` | Editable club information |
| `GET /api/public/{path}` | Strict whitelist of public read-only page HTML for Pages; never private RSVP/admin routes |
| `GET /health` | Database connectivity check |

## Admin routes

`/admin/login` is the only unauthenticated admin route. All other admin handlers verify the signed session; all writes verify CSRF. Cookies are HTTP-only, SameSite=Lax, and Secure in production. No attendee lists appear in the public API.

| Route | Purpose |
| --- | --- |
| `GET/POST /admin/login` | Sign in with generic failure message and rate limiting |
| `POST /admin/logout` | Clear signed session |
| `GET /admin` | Current book, upcoming events/counts, recent RSVPs/news |
| `GET /admin/content/{books|events|news}` | Manage records |
| `GET/POST /admin/edit/{kind}/{id}` | Create (`id=0`) or edit; optional validated image upload |
| `POST /admin/delete/{kind}/{id}` | Remove a record |
| `POST /admin/duplicate/{event_id}` | Copy event fields, start closed, no copied RSVPs |
| `GET /admin/rsvps/{event_id}` | Private guest list and response counts |
| `GET /admin/rsvps/{event_id}/export/{csv|xlsx}` | Download private attendance data |
| `GET/POST /admin/about` | Edit About page sections |
