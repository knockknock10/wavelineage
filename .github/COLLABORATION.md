# WaveLineage collaboration boundaries

This repository is the shared integration point for the MLH Hacktoberfest 2026 Best Open-Source AI Project submission.

## Workstream ownership

- `main`: integration branch. Only reviewed, working changes belong here.
- `gpt2/frontend`: GPT 2 frontend and UX workstream. Keep changes under `apps/web/` and `docs/frontend/`.
- GPT 1 owns the Python audio pipeline, HuBERT/MFCC feature encoders, alignment, cryptographic manifests, FastAPI, backend tests, and final integration.

## Product contract

WaveLineage is a local-first source-relative speech excerpt alignment toolkit. It compares a candidate excerpt to a user-designated reference recording and maps candidate windows to reference timestamps using learned speech representations and constrained sequence alignment. SHA-256 and Ed25519 bind the reference, normalized audio and feature index in a local source manifest.

It does not prove that the reference is authentic, that a speaker is who they claim to be, that a statement is true, or that a candidate is fabricated.

## API ownership

The current local backend contract is documented in `docs/API_CONTRACT.md` in the GPT 1 working tree. The frontend must not invent or silently change endpoints. Confirm the contract before integration.

Core routes:
- `GET /api/v1/health`
- `GET /api/v1/model`
- `GET /api/v1/identity`
- `POST /api/v1/sources`
- `GET /api/v1/sources`
- `GET /api/v1/sources/{source_id}`
- `GET /api/v1/sources/{source_id}/manifest`
- `POST /api/v1/align`
- `GET /api/v1/runs/{run_id}`
- `GET /api/v1/runs/{run_id}/report?format=json|html`
- `POST /api/v1/verify-manifest`

## Evidence and quality rules

- Never describe synthetic tests as real-model evaluation.
- Never publish uncalibrated cosine similarities as probabilities.
- Never claim the tool detects all deepfakes or establishes real-world authenticity.
- Do not commit audio recordings, model weights, private signing keys, API keys, local datasets, or generated feature indexes.
- Run tests/builds and report exact results; do not claim tests that were not run.
