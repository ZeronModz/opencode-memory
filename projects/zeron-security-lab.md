# Project: Zeron Security Lab

## Status: Backend Deployed ✅

## Repository
- Local: `/storage/emulated/0/Download/bypass-testing/`
- Files: 85+
- Backend: Python FastAPI
- Android: Kotlin + Jetpack Compose

## Backend
- **URL**: https://backend-production-cd11.up.railway.app
- **Docs**: https://backend-production-cd11.up.railway.app/docs
- **Health**: https://backend-production-cd11.up.railway.app/api/v1/health
- **Database**: SQLite (Railway volume)
- **Status**: Online, all endpoints verified

## Railway
- Account: Im Hasan2 (im.hasan.op.2@gmail.com)
- Project: backend (ID: f2a44ec8-526d-42af-be4c-005f4f4a6119)
- Region: sfo
- Database: SQLite (Postgres also available)

## Android App
- Package: com.zeron.securitylab
- Min SDK: 26
- Target SDK: 35
- Needs Android Studio to build (no SDK on Termux)

## API Endpoints
| Endpoint | Status |
|----------|--------|
| GET /api/v1/health | ✅ |
| GET /api/v1/system/info | ✅ |
| GET /api/v1/system/capabilities | ✅ |
| POST /api/v1/web/fetch | ✅ |
| POST /api/v1/web/render | ✅ |
| POST /api/v1/web/screenshot | ✅ |
| POST /api/v1/api/request | ✅ |
| POST /api/v1/api/assert | ✅ |
| GET /api/v1/targets | ✅ |
| POST /api/v1/targets | ✅ |
| DELETE /api/v1/targets/{id} | ✅ |
| GET /api/v1/history | ✅ |
| GET /api/v1/cloudflare/health | ✅ |
| POST /api/v1/cloudflare/test | ✅ |
| GET /api/v1/recaptcha/health | ✅ |
| POST /api/v1/recaptcha/test | ✅ |

## Third-Party Integrations
- CloudflareBypassForScraping (adapter layer, MIT license)
- GoogleRecaptchaBypass (adapter layer, official test keys)

## Last Updated: 2026-09-15
