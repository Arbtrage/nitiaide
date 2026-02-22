# Changes Summary

| File | Change | Why |
|------|--------|-----|
| `backend/Dockerfile` | `libgdk-pixbuf2.0-0` → `libgdk-pixbuf-2.0-0` | Package renamed in Debian trixie |
| `backend/Dockerfile` | Added `pkg-config` | Required to build `pycairo` from source |
| `backend/Dockerfile` | Added `libcairo2-dev` | Cairo headers needed for `pycairo` compilation |
| `backend/requirements.txt` | `uvicorn[standard]` → `uvicorn` | `[standard]` pulls `uvloop` which breaks `unstructured_client`'s `nest_asyncio.apply()` |
| `backend/entrypoint.sh` | Added `--loop asyncio` to uvicorn | Prevents uvloop from being used even if installed as a transitive dep |
| `backend/app/main.py` | `minPoolSize=5` → `minPoolSize=0` | Reduces noisy PyMongo background pool-maintenance tracebacks against Atlas |
| `backend/app/main.py` | Removed `socketTimeoutMS=20_000` | Global socket timeout was causing `_OperationCancelled` errors in PyMongo background threads |
| `backend/app/routers/chat.py` | Added `Response` import, `response_class=Response`, return `Response(status_code=204)` | FastAPI 0.115 asserts 204 must not have a response body |
| `backend/app/routers/ingest.py` | Same 204 fix as chat.py | Same FastAPI 0.115 assertion |
| `backend/app/routers/reports.py` | Same 204 fix as chat.py | Same FastAPI 0.115 assertion |
| `frontend/tsconfig.json` | Added `"target": "ES2015"` | `for..of` over `Map.keys()` requires ES2015+ target |
| `frontend/Dockerfile` | Added `RUN mkdir -p public` before build | `COPY --from=builder /app/public` fails if directory doesn't exist |
| `frontend/lib/api.ts` | `API_BASE` defaults to `""` in browser, `http://backend:8888` on server | Allows same-origin API calls through nginx proxy |
| `docker-compose.yml` | Removed `version: "3.9"` | Obsolete attribute, Docker warns about it |
| `docker-compose.yml` | Removed `ports` from `backend` and `frontend` | Only nginx should be publicly exposed |
| `docker-compose.yml` | Added `nginx` service on port `3000:80` | Single entrypoint reverse proxy for both frontend and backend |
| `nginx/default.conf` | Created | Routes `/api/*` → backend:8888, everything else → frontend:3000 |
| `nginx/Dockerfile` | Created | Bakes nginx config into image so no bind-mount needed on EC2 |
