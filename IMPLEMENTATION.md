# Current delivery: static GitHub Pages

The local FastAPI/Jinja application remains the editor. scripts/export_static.py renders an explicit set of public pages from books, events, published news, and About settings into docs/content.json. Only referenced public images and generated calendar downloads accompany the static shell.

The browser reads its own content.json and uses hash routes for repository-subpath compatibility. Search and category/year/genre filters run locally. Online RSVP forms and the public admin link are absent; events say Coming soon.

GitHub publishes main/docs using branch deployment. No backend variables, database credentials, Render service, or paid-account connection are involved. Changes in the local editor require re-exporting and uploading docs.

The original server-side models and authenticated workflows are retained for possible future hosted functionality. No local data migration or credential change was performed.
