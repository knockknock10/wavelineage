# GPT 2 frontend implementation notes

## Branch and ownership

- Repository: `knockknock10/wavelineage`
- Branch: `gpt2/frontend`
- Files owned by this workstream: `apps/web/` and `docs/frontend/` only.
- The shared GitHub base was documentation-only when this implementation was prepared; backend behavior has not yet been verified against a running service.

## Included in the local implementation bundle

- React/Vite application shell with Source Library, Seal Source and Verify Candidate flows.
- Live API connectivity probe for health, model and installation identity.
- Source list loading and refresh with explicit offline/error/empty states.
- Multipart source registration and candidate alignment requests.
- Explicitly confirmed source deletion through the newly documented DELETE endpoint, with no secure-erasure promise.
- Browser-only waveform preview generated from the selected local file, explicitly labeled preview-only.
- Candidate-to-source interval display driven only by API-reported segments.
- Separate manifest signature, exact-file integrity and AI alignment presentation.
- Threshold profile status warnings and explicit caveats.
- JSON/HTML report links using the documented report route.
- Node built-in unit tests for duration precision, source normalization, segment mapping and API errors.
- Frontend gap register asking GPT 1 for typed API response examples and security/temporal semantics.

## Deliberate non-features

- No mock source list or mock alignment result.
- No fabricated upload progress or internal model stages.
- No probability language for cosine similarity.
- No "fake", "forged" or malicious-intent classification.
- No trust assertion based only on a public key supplied beside its signature.
- No assertion that deleting a managed folder securely erases media from SSD wear-leveling, snapshots or backups.

## Validation status

- `npm test` (`node --test tests/*.test.js`): **7/7 passed**.
- JSX/JS syntax parse using the installed TypeScript transpiler: **6 source files parsed without syntax diagnostics**. This is a syntax check, not a Vite production build.
- `npm run build`: **not completed** — Vite is not installed in the preparation environment. Do not treat this as a successful production build.
- Backend integration is pending a published backend source tree and representative API response fixtures. The public repository tree was documentation-only when this implementation was prepared.

## Applying the bundle

Extract the provided `WaveLineage-GPT2-Frontend.zip` at the WaveLineage repository root while checked out on `gpt2/frontend`. It contains the paths `apps/web/` and `docs/frontend/`. Review the diff before committing; do not overwrite files created by GPT 1 outside these owned paths.
