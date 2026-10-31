# WaveLineage API contract — v0.1

All endpoints are intended for loopback/local use. Uploads are multipart; responses are JSON unless otherwise noted.

## Health and model

- `GET /api/v1/health`: process/storage state, encoder availability and threshold-profile status.
- `GET /api/v1/model`: encoder identity, resolved revision after load, feature dimension, frame stride, thresholds and profile status/digest.

## Installation identity

- `GET /api/v1/identity`: installation-local Ed25519 public key, SHA-256 fingerprint and trust-scope statement. Clients that need to retain provenance should pin this fingerprint out of band.

## Sources

- `POST /api/v1/sources`: multipart fields `file`, optional `label`; creates a signed reference manifest and feature index.
- `GET /api/v1/sources`: list source summaries.
- `GET /api/v1/sources/{source_id}`: metadata and integrity checks.
- `GET /api/v1/sources/{source_id}/manifest`: public manifest, base64 signature and public key PEM.
- `DELETE /api/v1/sources/{source_id}`: remove the local source folder and its associated alignment run reports. This is application-level deletion, not guaranteed secure erasure from SSD wear-leveling, filesystem snapshots or external backups.

## Alignment

- `POST /api/v1/align`: multipart `source_id`, `file`; returns candidate-to-source window alignment.
- `GET /api/v1/runs/{run_id}`.
- `GET /api/v1/runs/{run_id}/report?format=json|html`.

Each alignment segment includes candidate query start/end, source start/end (nullable), mean cosine similarity and status (`matched`, `uncertain`, `unmatched`). Scores are not probabilities. Unmatched segments have null source timestamps. Discontinuities are observations, not proof of intent or manipulation.

## External manifest verification

`POST /api/v1/verify-manifest` JSON body:

```json
{
  "manifest": {},
  "signature_base64": "...",
  "public_key_pem": "-----BEGIN PUBLIC KEY-----\\n..."
}
```

This checks the signature and whether the manifest's embedded public-key fingerprint matches the supplied public key. It does not establish the real-world identity of the key holder or trusted wall-clock time.

## Threshold profiles

A profile is created by `wavelineage calibrate-thresholds`, then evaluated on a separate held-out set with `wavelineage evaluate-threshold-profile`. The API only applies a profile when `WAVELINEAGE_THRESHOLD_PROFILE` points to its JSON file and the active encoder exactly matches model ID, resolved revision, representation, feature dimension and frame rate.

Loading a profile means calibration-derived thresholds are in use. It does not imply held-out test success or forensic fitness. Alignment reports contain `scoring.threshold_profile_id`, `threshold_profile_sha256`, `threshold_profile_status` and an explicit warning. Without a profile, thresholds are exploratory and `scoring.threshold_calibrated=false`.

## Error shape

Expected upload/model/source failures return structured FastAPI `detail` objects with codes such as `FILE_TOO_LARGE`, `AUDIO_INVALID`, `MODEL_UNAVAILABLE`, `SOURCE_NOT_FOUND`, `SOURCE_INTEGRITY_FAILED`, `SOURCE_DELETE_REFUSED` and `THRESHOLD_PROFILE_INVALID`.

## Trust and limitations

- A valid signature proves the manifest bytes were signed by the corresponding key and have not changed. It does not prove the recording is authentic.
- A source-relative match does not prove speaker identity, capture context or statement truth.
- A failed match is not proof of forgery.
- Initial model scope is English speech with `facebook/hubert-base-ls960`.
- This API contract describes the current local MVP and is not a frozen v1 compatibility guarantee.
