# Design considerations

| Constraints |
| ----------- |
| HIPAA-covered entity. PHI handling is non-negotiable. |
| Patients range from tech-native to elderly, low-bandwidth mobile. |
| Some patients will have sparse files (recent immigrants, 18–21 age group). |
| The system must support re-verification and manual review escalation. |
| Downstream systems (scheduling, billing, EHR) need to query verification status. |
| Audit trail required for compliance. |
