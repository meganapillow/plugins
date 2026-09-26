# Static GitHub Pages validation

- 8 automated tests passed, including all previous local-app flows and new static-export privacy checks.
- Exporter ran successfully using the existing Mac project’s Python environment and database.
- Export contains 16 public pages, 3 calendar downloads, and no referenced uploaded images in the current demo content.
- Public JSON contains no accounts, password hashes, RSVP records, private RSVP tokens, admin routes, or unpublished news. Only images referenced by public content are copied.
- Browser verified standalone homepage, event Coming Soon display, and working bookshelf search with no console errors during those checks.
- Public frontend loads only its local content.json; no backend connection or Render account is required.
- Calendar website URLs point to https://meganapillow.github.io/KWBC_test/#/events/ID.
- Local database, login, environment settings, and uploads were preserved.
- GitHub publishing is pending upload of docs/ and enabling main → /docs in Pages settings. The browser available to the assistant was signed out of GitHub; no repository changes or live deployment were performed.
