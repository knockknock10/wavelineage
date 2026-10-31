# Frontend handoff

## Repository and branch
- Repository: https://github.com/knockknock10/wavelineage
- Frontend branch: `gpt2/frontend`
- Main is the integration branch. Do not push frontend changes to main directly.
- GPT 2 ownership is limited to `apps/web/` and `docs/frontend/`.

## Current backend routes
See [API_CONTRACT.md](../API_CONTRACT.md). Main routes:
- `GET /api/v1/health`
- `GET /api/v1/model`
- `GET /api/v1/identity`
- `POST /api/v1/sources` (multipart `file`, optional `label`)
- `GET /api/v1/sources`
- `GET /api/v1/sources/{source_id}`
- `GET /api/v1/sources/{source_id}/manifest`
- `POST /api/v1/align` (multipart `source_id`, `file`)
- `GET /api/v1/runs/{run_id}`
- `GET /api/v1/runs/{run_id}/report?format=json|html`

## Required UI states
1. Local Source Library: list sealed references, model identity, manifest integrity/signature status.
2. Seal Source: file upload, duration/metadata, real stage progress, error handling.
3. Verify Candidate: source picker, candidate upload, alignment result.
4. Evidence timeline: query/candidate time mapped against source time; distinguish matched, uncertain, unmatched. Show discontinuities as observations only.
5. Report view/export: include model revision, profile digest/status, exact-file match, manifest signature/integrity, caveats.

## Important frontend semantics
- `GET /api/v1/model` may initially return `threshold_profile_status=pending_encoder_validation` when an env profile is configured but the ML encoder has not loaded. Do not display this as "validated".
- `calibration_loaded_heldout_not_certified` means calibration thresholds were loaded; it does NOT mean the profile passed a held-out test.
- When `threshold_calibrated=false`, show thresholds as exploratory/uncalibrated.
- Cosine similarity is not a probability.
- Exact-file identity, manifest signature validity and semantic alignment are three different things. Show them separately.
- Do not label unmatched audio "fake" or "forged".
- Do not imply reference authenticity, speaker identity, statement truth or capture context.
- Do not fake upload progress or show mock alignment as real model output. If mocks are used during frontend work, label them conspicuously.

## Visual direction
Professional audio-forensics workbench, neutral dark interface, waveform timeline, accessible keyboard controls and responsive desktop-first layout. The timeline must communicate source timestamps and candidate timestamps clearly, not just similarity bars.
