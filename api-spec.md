# Integration and API surface
## 11. Verification API gateway / façade
- **Role:**
    - Single entry point for mobile app and downstream systems.
- **Responsibilities:**
    - REST/JSON APIs for:
        - Start verification workflow
        - Upload document images and selfies.
        - Poll verification status.
        - Query current verification level and history.
    - Downstream APIs:
        - "Get current verification level for patient X"
        - "Get last verification method and timestamp."
        - "Subscribe/poll for changes in verification level."
## 12. Eventing / notification service
- **Role:**
    - Notify internal systems of verification changes.
- **Responsibilities:**
    - Emit events like VerificationCompleted, VerificationElevated, VerificationRevoked.
    - Allow downstream services to react (e.g., unlock features, log risk events).
    - Optional: internal notification to manual review team when new cases arrive.
