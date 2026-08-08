# Learned: New Vercel Python runtime — rewrite + StripRewritePrefix (2026-08-08)

## Topic: Deploying Flask to Vercel (2026, CLI >= 58) — regression
- Vercel CLI 58.x + newer runtime: internal rewrites route requests using the REWRITTEN DESTINATION path, not the original path. So `rewrites: [{source: "/api/(.*)", destination: "/api/index.py"}]` passes PATH_INFO=`/api/index.py` to Flask → 404 on every `/api/*` route (only `/` works).
- FIX RECIPE (2 parts, both needed):
  1. `vercel.json`: destination MUST include `$1`: `"/api/index.py/$1"`.
  2. Python: wrap app with WSGI middleware that strips the prefix:
     ```python
     app.wsgi_app = StripRewritePrefix(app.wsgi_app)   # prefix="/api/index.py"
     ```
- Older Temporary-Gmail-Vercel-Simple already had this pattern (StripRewritePrefix) — this is why it worked; zeron-gmail (copied earlier version) didn't.

## HTML→text extraction in Flask email API
- Walk MIME parts; prefer text/plain, else text/html.
- Must strip `<style>`/`<script>` blocks FIRST (regex `(?is)<(style|script)...>.*?</...>`) or CSS text leaks into the readable body.
- `html.unescape()` for entities; collapse runs of spaces/newlines.