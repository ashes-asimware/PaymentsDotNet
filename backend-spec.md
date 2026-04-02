# Core backend services notes

## 1. Identity Verification Orchestration Service
### **Role:** 
Central brain that coordinates document capture, biometric checks, rules, and state transitions between assurance levels.

### **Responsibilities:**  
- Manage verification workflows per patient and per device.  
- Call document analysis, face match, liveness, and rules engine.  
- Persist verification outcomes and reasons.  
- Expose APIs for mobile app and downstream systems.

## 2. Verification state service
### **Role:** 
Authoritative source of “current verification level” for each patient session and device.

### **Responsibilities:**
- Maintain per-patient, per-device verification state (Level 0/1/2, timestamps, expiry).
- Provide low-latency read APIs for downstream systems (scheduling, billing, EHR).
- Enforce policies for when elevation is required for specific actions.

## 3. Document capture and analysis service
### **Role:** 
Handle ingestion and processing of government IDs and other documents.
### **Responsibilities:**
- Secure upload endpoints for mobile app (front/back of ID, insurance card if needed).
- Image quality checks (blur, glare, cropping).
- Document parsing (MRZ, barcodes, OCR of name/DOB/address).
- Fraud signals (tampering heuristics, mismatched fields).
- Minimize storage: retain only derived attributes + redacted thumbnails or hashed artifacts per policy.

## 4. Biometric and liveness service
### **Role:** 
Perform selfie capture, face matching, and liveness checks.
### **Responsibilities:**
- Secure selfie upload and processing.
- Face match between selfie and document photo.
- Liveness detection (active or passive).
- Store only biometric templates or derived scores where necessary, with strict retention and encryption.

## 5. Rules and decision engine
### **Role:** 
Automated decisioning for verification outcomes and routing to manual review.
### **Responsibilities:**
- Define rules for Level 1 vs Level 2 (e.g., score thresholds, document type, age bands).
- Auto-approve, auto-reject, or “manual review required” decisions.
- Generate structured reason codes for audit and downstream analytics.
### **Automation vs manual (suggested):**
- <b>Automated:</b> Clear matches, high-quality documents, strong liveness scores.
- <b>Manual:</b> Borderline scores, conflicting data, unusual patterns (e.g., repeated attempts from same device).

## 6. Manual review and case management service
### **Role:**
Manage verification cases that require human review.
### **Responsibilities:**
- Create cases when rules engine flags “manual review.”
- Assign to reviewers, track status (open, in review, approved, rejected).
- Record reviewer actions, notes, and final decisions.
- Integrate with verification state service to update assurance level.
### **Separate app: 
- Expose APIs for a dedicated back-office UI.**

## 7. Session and policy enforcement adapter
### **Role:** 
Bridge between Auth0 sessions and internal verification state.
### **Responsibilities:**
- Map Auth0 subject IDs to internal patient IDs.
- Decorate access tokens or session context with current verification level.
- Provide policy checks (e.g., “is Level ≥ 1 required for this API?”) for downstream services.
