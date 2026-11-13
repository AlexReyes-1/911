# 911 Bench: project instructions for Claude Code

An interactive 3D explorer for a Porsche 911. Read SPEC.md before making changes. It is the source of truth for scenes, interactions, the engine parts list, the content model, and milestones.

## Stack
- Frontend: React, Vite, TypeScript, React Three Fiber, Drei, Zustand (in `web/`)
- Backend: FastAPI, Pydantic (in `api/`)
- Content: JSON in `content/` (vehicle, scenes, parts). Content is data, not React code.
- 3D models: stylized glTF files in `web/public/models/`, built by a reproducible Blender script in `tools/blender/`

## Commands
- Frontend dev: `cd web && npm install && npm run dev`
- Frontend tests: `cd web && npm test`
- Frontend build: `cd web && npm run build`
- Backend dev: `cd api && python -m venv .venv && . .venv/bin/activate && pip install -r requirements.txt && uvicorn main:app --reload`
- Backend tests: `cd api && pytest`

Run the relevant tests and the build before reporting a task as done.

## Conventions
- Part IDs in `content/parts/engine.json` must match the named mesh nodes in the engine glTF. Change both together, or not at all.
- Every part has `variant`: `all`, `air-cooled`, `water-cooled`, or `turbo`. Turbo-only parts appear only when the Turbo toggle is on.
- All artwork and models are original. Do not add Porsche logos, crests, photos, or Porsche-owned assets.
- Record the source and licence of every model in `content/ASSETS.md`.
- Keep the footer disclaimer: the project is not affiliated with Porsche AG.

## Scope rules
- Milestones in SPEC.md run in order. M0 (models) comes before any scene work.
- Do not commit or push unless asked.
- Do not change the scope in SPEC.md without asking.
