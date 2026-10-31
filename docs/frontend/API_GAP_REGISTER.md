# API integration gap register — frontend review

Reviewed against the live `docs/API_CONTRACT.md` and `docs/FRONTEND_HANDOFF.md` on 2026-10-31. This is a request for concrete schemas, not a proposal to silently change backend behavior.

## Blocking or high-priority contract gaps

1. **Response schemas are not fully specified.** The contract lists endpoints and a few segment fields, but it does not give JSON examples or required/optional fields for health, model, identity, source summaries, source detail, source manifest, alignment response, run detail, or report. Please add versioned JSON examples and define whether each response is an object or envelope.
2. **Alignment execution semantics are unclear.** `POST /align` says it returns candidate-to-source alignment, while a `GET /runs/{run_id}` route also exists. Define whether alignment is synchronous, how queued/running/completed/failed states are represented, and whether clients should poll. Include a terminal state and a stable run identifier.
3. **Timeline time base is underspecified.** State that all query/source interval fields are seconds from the beginning of the decoded candidate/reference timeline, define precision, inclusive/exclusive end semantics, and how model feature-frame offsets/receptive fields are converted to audio time. Avoid frame-index-to-seconds shortcuts that ignore encoder offsets.
4. **Multiple/ambiguous matches need an explicit representation.** Define ordering, possible overlapping intervals, repeated-phrase ambiguity, ties, and how uncertain mappings differ from unmatched mappings. If an interval has two plausible source locations, the schema should preserve that ambiguity rather than collapsing it into one arbitrary match.
5. **Scoring/calibration fields need a complete schema.** The frontend handoff calls out `threshold_profile_status`, `threshold_profile_id`, profile digest and calibration state, but the contract does not define their types, where each field appears, or allowed statuses. Define score direction/range and make clear that cosine similarity is not a probability.
6. **Source integrity and signatures need separate exact fields.** Define source-byte digest, normalized-audio digest, feature-index digest, manifest schema version, signature algorithm, canonicalization version, public-key fingerprint algorithm and key ID. Specify precisely what bytes are signed.
7. **Key trust is not the same as signature verification.** `/verify-manifest` currently accepts a manifest, signature and public key together, and verifies that signature and embedded fingerprint agree with that supplied key. This detects alteration relative to the supplied key but does not establish that the user trusts that key. Define a trusted/pinned-key comparison workflow (out-of-band fingerprint) separately; do not show a self-supplied key as verified identity.
8. **Upload constraints and local-service security need definition.** Specify supported formats, max file size/duration, decoder timeout, error codes, and whether the service binds only to loopback by default. Define CORS policy for the Vite development origin and avoid enabling broad origins by default.
9. **Deletion behavior needs a typed response.** `DELETE /sources/{source_id}` is now documented as removing the managed source folder and associated run reports, without guaranteeing secure erasure. Define its success response, behavior when a run is active, dependent report handling, and how the API prevents deleting a source while an alignment request is reading it. The UI must use an explicit confirmation and avoid secure-erasure claims.
10. **Report format details need definition.** Specify JSON/HTML schemas and whether report URLs are directly downloadable, same-origin, and safe to open in a new tab. Include model revision, preprocessing config, run ID, threshold profile digest/status, source key fingerprint and limitations.

## Current frontend implementation choices

- Uses `GET /health`, `/model`, `/identity`, `/sources` on startup.
- Uses multipart `file` (plus optional `label`) for `POST /sources`; uses multipart `source_id` and `file` for `POST /align`.
- Accepts direct arrays or the `sources`/`items`/`results` list envelopes while the backend schema is finalized.
- Normalizes the documented segment fields: `query_start`, `query_end`, `source_start`, `source_end`, `mean_cosine_similarity`, `status`.
- If a response contains a `run_id` but no segments, it performs one read of `GET /runs/{run_id}`. It does not invent a polling cadence or terminal states.
- Uses the report endpoint documented in the contract for `json` and `html` formats, but downloads reports rather than opening backend HTML inline until output-sanitization and content-disposition behavior is confirmed.
- Exports the manifest endpoint response as a JSON file; the exported key/signature materials remain subject to the trust limits documented by the backend.
- Calls `/verify-manifest` only when all three documented inputs (`manifest`, `signature_base64`, `public_key_pem`) are present.

These are compatibility tolerances for the MVP, not replacements for a strict, documented v1 schema.
