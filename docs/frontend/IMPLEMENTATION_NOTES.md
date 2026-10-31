# GPT 2 frontend implementation notes

## Branch and ownership

- Repository: `knockknock10/wavelineage`
- Branch: `gpt2/frontend`
- Files owned by this workstream: `apps/web/` and `docs/frontend/` only.
- The current shared GitHub base commit was `92afe7bbd436e3a3d25411cd4fda685b826805d7` when this implementation began.

## Included in the local implementation bundle

- React/Vite application shell with Source Library, Seal Source and Verify Candidate flows.
- Live API connectivity probe for health, model and installation identity.
- Source list loading and refresh with explicit offline/error/empty states.
- Multipart source registration and candidate alignment requests.
- Explicitly confirmed source deletion through the newly documented DELETE endpoint, with no secure-erasure promise.
- Portable JSON export of the API-returned signed manifest from the source library and seal-success view.
- Browser-only waveform preview generated from the selected local file, explicitly labeled preview-only.
- Candidate-to-source interval display driven only by API-reported segments; malformed, negative, zero-length or partial intervals are not rendered as valid mappings. A `matched` status without a usable source interval, or an `unmatched` status with one, is downgraded to uncertain.
- Separate manifest signature, exact-file integrity and AI alignment presentation; source changes clear any previous manifest-verification result.
- Per-result encoder revision, method label, threshold-profile identifier/digest and explicit calibration flag display.
- Download-first report action until backend HTML escaping/content-disposition guarantees are specified.
- Threshold profile status warnings and explicit caveats.
- Node built-in unit tests for duration precision, source normalization (including status normalization), segment mapping and API errors.

## Deliberate non-features

- No mock source list or mock alignment result.
- No fabricated upload progress or internal model stages.
- No probability language for cosine similarity.
- No "fake", "forged" or malicious-intent classification.
- No trust assertion based only on a public key supplied beside its signature.

## Validation status

- `npm test` (`node --test tests/*.test.js`): **7/7 passed**.
- JSX/JS syntax parse using the installed TypeScript transpiler: **6 source files parsed without syntax diagnostics**. This is a syntax check, not a Vite production build.
- `npm run build`: **not completed** — Vite is not installed in the preparation environment. An `npm install --ignore-scripts --no-audit --no-fund` attempt timed out before dependencies were available. Do not treat this as a successful production build.
- Backend integration is pending a published backend source tree and representative API response fixtures. The public repository tree was documentation-only when this implementation was prepared.

## Applying the bundle

Extract the supplied `WaveLineage-GPT2-Frontend.zip` at the WaveLineage repository root while checked out on `gpt2/frontend`. It contains only GPT 2-owned paths: `apps/web/` and `docs/frontend/`. Review the diff before committing; do not overwrite files created by GPT 1 outside these owned paths.
