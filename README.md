# WaveLineage

**Map every excerpt back to its source.**

WaveLineage is a local-first toolkit for source-relative speech excerpt alignment and provenance. A user registers a reference recording, then examines a candidate recording. The application extracts learned speech representations from both and attempts to map candidate windows onto intervals in that designated source. It reports matched, uncertain and unmatched windows, alternative source locations where repeated passages are plausible, and source-time discontinuities.

A local Ed25519 signing identity seals a manifest binding the original file bytes, normalized analysis waveform and feature index with SHA-256 digests. The signing private key remains in the local application data directory.

> **Scope boundary:** WaveLineage does not determine whether the reference depicts a real event, identify a speaker, prove that speech is true, detect every deepfake, or establish that unmatched audio is fabricated. It reports correspondence to a reference the user designated.

## Project status — 31 October 2026

This is an engineering prototype. The current test suite covers deterministic implementation mechanics and synthetic fixtures. **Real HuBERT checkpoint inference and real participant-recorded alignment have not yet been validated in the recorded build environment.** Do not present unit tests as learned-model accuracy results.

| Area | Status |
|---|---|
| Audio decoding and mono 16 kHz normalization | Implemented; SoundFile plus bounded FFmpeg fallback |
| HuBERT adapter | Implemented and lazy-loaded; real checkpoint inference not yet validated |
| Candidate-to-source alignment | Constrained subsequence alignment, overlapping windows and ambiguity alternatives |
| Provenance | SHA-256 digests and canonical JSON Ed25519-signed manifest |
| Local API | FastAPI health/model/identity/source/align/run/report/manifest-verification routes |
| Calibration and evaluation | Feature-level calibration, source/query group leakage checks and held-out evaluator |
| Raw-audio benchmark preparation | Clean crops, gain, noise, resampling, splice/reorder and optional negative trials |
| Frontend | React + Vite application; frontend unit tests |
| Real-world accuracy and robustness | Not established; no public performance percentages are claimed |

## How it works

1. Register a user-designated reference recording.
2. Normalize audio to mono 16 kHz and extract HuBERT frame representations.
3. Store file, normalized PCM and feature-index SHA-256 digests in a signed manifest.
4. Analyze a candidate recording using the same pinned encoder.
5. Align overlapping candidate windows to source intervals and classify them as matched, uncertain/ambiguous or unmatched.
6. Inspect candidate-time-to-source-time mapping, discontinuities, manifest integrity and signature status separately.

The default encoder is `facebook/hubert-base-ls960`, pretrained for English speech. Pin an exact Hugging Face commit using `WAVELINEAGE_MODEL_REVISION` for reproducible experiments. The source index and candidate must use the same model identity, representation, feature dimension and time base.

## Repository contents and implementation

The application implementation is being published from the `submission/wavelineage-complete` branch. The current repository's default branch contains the project handoff and API/frontend documentation. Until the complete application source is merged, treat this README as a project description—not as confirmation that the default branch is a runnable release.

## Intended local setup

Python 3.10+ is required. FFmpeg is recommended for additional compressed-media formats. The model extra uses PyTorch, Transformers and safetensors; the first real inference may download model weights.

Expected development workflow once the application source is present:

```bash
python -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
pip install -e '.[model,dev]'
uvicorn apps.api.main:app --host 127.0.0.1 --port 8000
```

Frontend development:

```bash
cd apps/web
npm install
npm run dev
```

The API is designed for loopback-only use. It has no authentication or multi-tenant access-control layer; do not expose it to a public network.

## Testing and evaluation

The recorded checks for the prepared implementation package were:

- Backend: **39 tests passed**.
- Frontend: **10 tests passed**.
- Python compile check: passed.
- Wheel build: passed.
- FastAPI health, OpenAPI and model-identity smoke checks: passed.
- Ruff lint: not run successfully because Ruff was unavailable.
- Frontend production build: not verified because dependency installation did not complete.
- Real HuBERT inference: not verified; checkpoint weights were unavailable in that environment.

These checks do not establish model accuracy, robustness, or forensic validity. Benchmarking should use independent source-speaker/session groups, held-out evaluation, localization metrics, and baselines such as MFCC/DTW and the MIT-licensed audfprint project. Report all model revisions, dataset splits and failure cases.

## Security, privacy and trust

- Recordings, feature indexes and signing keys are intended to remain in local application storage.
- A valid Ed25519 signature establishes mathematical validity for the included public key; it does not establish signer identity unless the key fingerprint is trusted out of band.
- Signatures do not prove genuine capture, trusted timestamps or factual truth.
- Exact-file identity, normalized waveform integrity, feature-index integrity and learned alignment are separate evidence dimensions.
- A failed match does not prove forgery or AI generation.
- Source deletion is not guaranteed secure erasure from SSD wear-leveling, snapshots or backups.

See [API Contract](docs/API_CONTRACT.md), [Frontend Handoff](docs/FRONTEND_HANDOFF.md), and the project documentation under `docs/`.

## Prior art and differentiation

Audio fingerprinting and excerpt matching predate WaveLineage. The project does not claim to be the first source-matching or audio-provenance system. Its hypothesis is that local-first, learned speech representations, inspectable candidate-to-source timing, uncertainty handling and split-safe edit benchmarks can provide a useful additional workflow. That hypothesis requires evaluation against established baselines.

## License

The project is intended to be released under Apache-2.0. Third-party model weights and dependencies retain their own licenses; review their model cards and notices separately.
