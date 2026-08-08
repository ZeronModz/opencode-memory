# Problem: /api/* 404 after deploy (new Vercel runtime) + HTML body empty

## Date: 2026-08-08
## Project: zeron-gmail.vercel.app (also Temporary-Gmail-Vercel-Simple pattern)

## Problem 1: HTML body empty
- Root cause: `body()` only read `text/plain` MIME part; HTML-only mails return "".

## Problem 2: after deploy, all `/api/*` → 404 (route not found)
- Root cause: NEW Vercel Python runtime + CLI 58.x now routes rewrites by the DESTINATION path. `vercel.json` rewrite `"/api/(.*)"` → `"/api/index.py"` passed PATH_INFO=`/api/index.py`, which matched no Flask route (and not `/api/`).
- Only `/` home route still worked.

## Solution (both)
1. `body()` fallback text/plain→text/html + `_strip_html` (strip style/script, unescape) + `body_html` field.
2. `vercel.json`: destination → `"/api/index.py/$1"`.
3. Add `StripRewritePrefix` WSGI middleware in `api/index.py`:
   ```python
   class StripRewritePrefix:
       def __init__(self, application, prefix="/api/index.py"):
           self.application = application; self.prefix = prefix
       def __call__(self, environ, start_response):
           path = environ.get("PATH_INFO", "")
           if path == self.prefix or path.startswith(self.prefix + "/"):
               environ["PATH_INFO"] = path[len(self.prefix):] or "/"
           return self.application(environ, start_response)
   app.wsgi_app = StripRewritePrefix(app.wsgi_app)
   ```

## Files Changed
- `api/index.py`, `vercel.json`

## Verification
- Live: `/api/generate/mixed` 200, `/api/read/<alias>` 200 with HTML OTPs extracted (910466/2271).