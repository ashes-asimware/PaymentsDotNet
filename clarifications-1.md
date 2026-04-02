# Key area clarifications for design

## 1.Scope of channels and touchpoints
|          |                                 |              |
| :-------- | :------------------------------- | :------------ |
| Channels | Will verification occur in web app, native mobile apps, call center/IVR,<br> in-clinic kiosks, or all of the above? | Mobile app |
| Actors | Is this patient-only, or do caregivers/proxies (parents, legal guardians,<br> POA) also need identity verification with linked access? | Patient only |
| Geography | Is this US-only, or do you need to consider international patients<br> and IDs? | U.S. only |

## 2. Identity sources and proofing methods
|          |                                 |              |
| :-------- | :------------------------------- | :------------ |
| Documents | Are you open to collecting and processing government ID<br> images (DL, passport), insurance cards, etc., or do you want to<br> avoid document capture? | Documents need to be captured.<br> Also, plan for biometrics and facial ID. |
| Data sources | Can we use internal data (MRN, prior visit history, address,<br> phone, DOB) and external data sources (credit header data,<br> public records, payer eligibility APIs), or must this be internal-only? | do not use internal data |
| Biometrics | Are biometrics (face match, voice print, device biometrics via<br> platform APIs) in or out of scope? | recommend low storage |

## 3. Authentication vs. identity verification
|          |                                 |              |
| :-------- | :------------------------------- | :------------ |
| Auth baseline | Do you already have (or plan to have) a standard auth layer (username/password, OAuth/OIDC, SSO), and this IDV system<br> sits on top of that? | Auth0, prefer OTP over password |
| MFA | Are you willing to require MFA (SMS, email OTP, app-based, WebAuthn) for higher assurance levels, or must MFA be optional? | Not optional |
| Session model | How long are typical patient sessions, and do you allow long<br>-lived refresh tokens or “remember this device” behavior? | Default functionality, i.e.long lived session, refresh token and remember device |

## 4. Assurance levels and policy
|          |                                 |              |
| :-------- | :------------------------------- | :------------ |
| Levels | Do you have a target model in mind (e.g., LOW / MEDIUM / HIGH<br> assurance, or NIST IAL/AAL-style), or should I propose one? | Propose one |
| Triggers | What events should automatically trigger re-verification or elevation?<br> (e.g., changing address, viewing sensitive notes, high-dollar payments,<br> telehealth visit start) | Not needed |
| Failure handling | For patients who cannot be auto-verified (sparse files, mismatched<br> data), what’s the preferred fallback—manual review, in-clinic verification, or limited-access mode? | Manual review |

## 5. Manual review and operations
|          |                                 |              |
| :-------- | :------------------------------- | :------------ |
| Review team | Do you have (or plan to have) a back-office team to handle manual<br> verification queues, or does this need to be minimal-staff/automation-heavy? | Limited involvement - suggest breakdown of automated rules vs manual checks |
| SLA | Any expectations on turnaround time for manual review (minutes, hours,<br> next business day)? | Next business day |
| Tooling | Should the same platform provide a back-office console for reviewers,<br> or can that be a separate internal app? | Out of scope |

## 6. Integration with downstream systems
|          |                                 |              |
| :-------- | :------------------------------- | :------------ |
| Integration style | Do downstream systems prefer to pull verification status<br> (APIs, event streams) or should we push events<br> (e.g., via message bus, webhooks)? | API |
| Granularity | Do they need just a single “verification level” flag, or more<br> detail (method used, timestamp, confidence score, reason codes)? | More detail (for audit) |
| Legacy systems | Any key systems that are especially constrained (e.g., old EHR<br> that can only store a small code or can only poll nightly)? | None, net new implementation |

## 7. Compliance, audit, and retention
|          |                                 |              |
| :-------- | :------------------------------- | :------------ |
| Audit depth | For each verification event, what must be auditable—inputs<br> used, decision outcome, rules fired, reviewer identity, before/after state? | Recommend |
| Retention | Any specific data retention/deletion policies for ID artifacts<br> (ID images, logs, decision data) beyond standard HIPAA minimums? | Best practices and cost optimized |
| Regulators/standards | Are you aiming to align with any specific frameworks<br> (e.g., NIST 800-63, HITRUST, SOC 2) beyond HIPAA? | HITRUST |

## 8. Patient experience and constraints
|          |                                 |              |
| :-------- | :------------------------------- | :------------ |
| Low-bandwidth | Are we allowed to design “graceful degradation” flows<br> (e.g., text-only, no image upload) for low-bandwidth users? | No graceful degradation |
| Accessibility | Any explicit requirements for accessibility (WCAG level,<br> language support, screen readers, large fonts)? | ADA compliant |
| Friction tolerance | How much friction is acceptable for high-risk actions?<br> For example, is a 30–60 second verification flow acceptable<br> before releasing sensitive clinical data? | Recommend |

