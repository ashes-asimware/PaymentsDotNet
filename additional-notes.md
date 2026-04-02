High-level assurance model and friction tolerance
Assurance levels (proposed):

Level 0 – Authenticated only: User is logged in via Auth0, no IDV yet. Access to low-risk, non-PHI features only.

Level 1 – Basic verified: Document captured + automated checks passed (no manual review). Access to most clinical/financial features.

Level 2 – Strong verified: Document + face match + liveness + higher rule thresholds and/or manual review. Access to all sensitive actions (e.g., high-dollar payments, sensitive notes).

Friction tolerance (recommended):

Initial onboarding: Allow 1–2 minute flow (document capture + selfie + OTP) once per device to reach Level 1.

Elevation to Level 2: Only when user attempts high-risk actions; allow up to ~60 seconds additional friction (extra selfie/liveness or queued manual review with “pending” state and limited access).

Core backend services
Identity verification orchestration service

Role: Central brain that coordinates document capture, biometric checks, rules, and state transitions between assurance levels.

Responsibilities:

Manage verification workflows per patient and per device.

Call document analysis, face match, liveness, and rules engine.

Persist verification outcomes and reasons.

Expose APIs for mobile app and downstream systems.

Verification state service

Role: Authoritative source of “current verification level” for each patient session and device.

Responsibilities:

Maintain per-patient, per-device verification state (Level 0/1/2, timestamps, expiry).

Provide low-latency read APIs for downstream systems (scheduling, billing, EHR).

Enforce policies for when elevation is required for specific actions.

Document capture and analysis service

Role: Handle ingestion and processing of government IDs and other documents.

Responsibilities:

Secure upload endpoints for mobile app (front/back of ID, insurance card if needed).

Image quality checks (blur, glare, cropping).

Document parsing (MRZ, barcodes, OCR of name/DOB/address).

Fraud signals (tampering heuristics, mismatched fields).

Minimize storage: retain only derived attributes + redacted thumbnails or hashed artifacts per policy.

Biometric and liveness service

Role: Perform selfie capture, face matching, and liveness checks.

Responsibilities:

Secure selfie upload and processing.

Face match between selfie and document photo.

Liveness detection (active or passive).

Store only biometric templates or derived scores where necessary, with strict retention and encryption.

Rules and decision engine

Role: Automated decisioning for verification outcomes and routing to manual review.

Responsibilities:

Define rules for Level 1 vs Level 2 (e.g., score thresholds, document type, age bands).

Auto-approve, auto-reject, or “manual review required” decisions.

Generate structured reason codes for audit and downstream analytics.

Automation vs manual (suggested):

Automated: Clear matches, high-quality documents, strong liveness scores.

Manual: Borderline scores, conflicting data, unusual patterns (e.g., repeated attempts from same device).

Manual review and case management service

Role: Manage verification cases that require human review.

Responsibilities:

Create cases when rules engine flags “manual review.”

Assign to reviewers, track status (open, in review, approved, rejected).

Record reviewer actions, notes, and final decisions.

Integrate with verification state service to update assurance level.

Separate app: Expose APIs for a dedicated back-office UI.

Session and policy enforcement adapter

Role: Bridge between Auth0 sessions and internal verification state.

Responsibilities:

Map Auth0 subject IDs to internal patient IDs.

Decorate access tokens or session context with current verification level.

Provide policy checks (e.g., “is Level ≥ 1 required for this API?”) for downstream services.
