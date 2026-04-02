# Data stores and audit/logging

## 8. Verification data store
### Role:
- Primary transactional store for verification workflows.</li>  
### Contents:
- Patient–device verification records (level, timestamps, methods used).
- Document metadata (type, issuing state, last verified date).
- Biometric decision metadata (scores, not raw images where avoidable).
### Design: 
- Encrypted at rest, PHI-aware schema, minimal PII duplication.

## 9. Artifact storage (low-storage, PHI-aware)
### Role: 
- Store sensitive artifacts with strict retention.
### Contents:
- Document images, selfies, and any derived templates.
### Approach:
- Encrypted object storage with short retention windows (e.g., keep raw images only until manual review window closes, then delete; retain hashes and decision metadata).
- Configurable retention policies per artifact type.

## 10. Audit and event log store
### Role: 
- Immutable, queryable audit trail for compliance and for HITRUST/HIPAA evidence.
### Audit depth (recommended):
- Who: patient ID, device ID, reviewer ID (for manual).
- What: action (capture, decision, elevation, rejection), previous and new verification level.
- When: timestamps for each step.
- How: methods used (doc type, biometric, rules version), scores and reason codes (non-raw).
- Where: IP, device fingerprint, app version (for security analytics).
### Implementation: 
- Append-only log (e.g., event store or WORM-like configuration) with indexing for audits.
