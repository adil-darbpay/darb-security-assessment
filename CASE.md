# Security engineering case study

Case study version 2.1. All details are fictional.

[Instructions and evaluation criteria](README.md)

## 1. Your assignment

You have been asked to review a customer service application. A contractor has made an unexpected production change, and support is reporting that a legitimate customer order was blocked. The operations lead needs your recommendation before the next change window.

Using the evidence below, explain what you would address first, how you would make two changes safely, and what you still need to investigate. Prepare your response for the application owner and IT operations team, following the five tasks in the README. Keep it focused: you are not being asked for a full audit or a complete redesign.

The systems, identities and events are invented for the exercise. They do not describe Darb.

## 2. Business requirements

| ID | Required outcome |
| --- | --- |
| R01 | Contractors may work in staging. Production changes require a named identity, an approved purpose and time-limited access. |
| R02 | Support staff may access only the confidential customer records assigned to their team. |
| R03 | Staff access to email, files and the staff portal requires a managed, encrypted device with screen lock. An office network location alone is insufficient. |
| R04 | Legitimate customer transactions must remain available, including names containing apostrophes. Login-abuse protection must continue to work. |
| R05 | Operations must retain a controlled administrative recovery route. Health checks must work without returning customer information. |
| R06 | Security changes require an owner, verification and a rollback decision. Ongoing access concerns may justify targeted containment before a permanent change is ready. |
| R07 | Workloads may access only the credentials needed for their function. Production must run the reviewed and approved release. |

Assume the application owner and IT operations lead can help validate a change in staging and through a limited production pilot. If your recommendation would interrupt service, explain why and how you would limit the impact.

## 3. System overview

| Path | Request or access flow |
| --- | --- |
| F01 Customer service | Customer → public load balancer and WAF → API → private database |
| F02 Staff portal | Staff browser → staff load balancer with identity-aware proxy (IAP) → staff application → database |
| F03 Deployment | CI pipeline → deployment service account → production resources |
| F04 Identity | Workforce identity directory → groups → cloud permissions and staff application access |
| F05 Collaboration | Staff browser → email and file services with device-context policies |

Customer authentication is handled by the application. IAP is enabled on the staff load balancer. Google Workspace provides the collaboration-control example; other cloud behavior is defined explicitly in the exhibits. Provider names and exact configuration syntax are not required in your response.

### Evidence handling

- Configuration records C01–C10 represent a **09:05 UTC snapshot**. Later events may change that state. C11 is a hypothetical design question.
- Configuration exports describe observed settings. Tickets and owner statements describe reported intent or activity. Neither automatically establishes the full incident outcome.
- Events are normalized excerpts from the same exercise day, not complete provider logs. Preserve the distinction between **confirmed**, **plausible**, and **not yet established** conclusions.
- Use the stated policy semantics. Do not invent missing settings or assume that every cloud evaluates permissions, rules or revocation in the same way.

## 4. Evidence exhibits

### Exhibit A — Identity and privileged access

**C01 · Group membership export**

| Group | Members |
| --- | --- |
| staff-access | employees |
| ops-prod | engineers, contractors |
| engineers | Engineer A, Engineer B |
| contractors | Contractor C |
| employees | Engineer A, Engineer B, Support S |

The support lead owns the contractors group and can change its membership without an additional approval. In this exercise, child-group members receive grants assigned to the parent after propagation. A complete inherited-policy inventory is not included.

**C02 · Permission export**

| Resource scope | Principal | Grant | Condition |
| --- | --- | --- | --- |
| Production project | ops-prod group | Compute Admin: broad administration of compute resources | None |
| Production project | ci-deploy service account | Editor: broad modification rights across services | None |
| Staff portal IAP | staff-access group | Permission to enter the protected application | None |

**C03 · Privileged access workflow**

Engineers are eligible to request production Compute Admin for up to eight hours. A justification is required; approval is not. The workflow records requests and their expiry. The collaboration suite's emergency super-admin account is shared and also used for routine administration. Its use has no dedicated alert.

### Exhibit B — Network and application protection

**C04 · Inbound firewall policy**

For this exhibit, **the lowest priority number is evaluated first and the first matching allow or deny determines the result**. No higher-level firewall policy overrides these rules. Other ingress is denied.

| Rule | Priority | Source | Target | TCP ports | Action | Logging |
| --- | ---: | --- | --- | --- | --- | --- |
| staff-proxy | 800 | Staff load balancer and approved health-check sources | staff-vm | 443 | Allow | On |
| legacy-admin | 900 | Any Internet address | staff-vm | 22, 443 | Allow | Off |
| block-direct-admin | 1000 | Any Internet address | staff-vm | 22, 443 | Deny | On |
| app-to-db | 1000 | 10.40.0.0/16 | database | 5432 | Allow | On |

The application subnet is `10.40.10.0/24`; engineering is `10.40.20.0/24`; both are within the shared network `10.40.0.0/16`. The database has no public endpoint. The management route needed by operations must be considered separately from application ingress.

**C05 · Staff application review extract**

| Item | Evidence supplied |
| --- | --- |
| Entry points | The staff load balancer has IAP enabled. Its backend, staff-vm, also has a public address and an HTTPS listener. |
| Identity handling | The application reads `X-Goog-Authenticated-User-Email`. Signed IAP identity-token validation is disabled in the reviewed handler. |
| Record access | The handler requires a recognized staff identity. An engineering note says customer-team authorization is in shared middleware; that middleware and its test results are not supplied. |
| Health endpoint | `/healthz` returns a basic service-health result and is used by approved health checkers. |

**C06 · Public WAF policy and support request**

This WAF policy is attached to the public load balancer only. In this exhibit, allow and deny actions end evaluation; lower priority numbers run first.

| Priority | Match | Action | Recorded purpose |
| --- | --- | --- | --- |
| 100 | Path starts with `/api/` | Allow | Temporary compatibility exception; no expiry recorded |
| 200 | Managed SQL injection signatures | Deny | Enforced, not preview |
| 300 | `/login` exceeds 200 requests per minute per source IP | Rate limit excess requests | Enforced, not preview |
| Last | Other requests | Allow | Default |

Support reports a rejected checkout with the customer name `O'Neil`. The product owner requests a same-day fix. No evidence has been supplied that all requests on the affected route are safe, or that the reported name is the complete cause of the signature match.

### Exhibit C — Workforce and collaboration security

**C07 · Device-context policy**

The policy applies to email and files. The test users have the required license and use supported clients. The staff-portal IAP grant has no device/access-level condition.

```text
Allow = office_network OR (managed_device AND disk_encrypted AND screen_lock)
```

The export does not include how stale or unknown device signals are handled. API and service-account access is outside this end-user policy extract.

**C08 · Collaboration control review**

| Control | Evidence and source |
| --- | --- |
| Staff sign-in | Named staff identities use MFA. This confirms a sign-in control, not every subsequent access decision. |
| Privileged authentication | SMS is still permitted for administrator sign-in. The shared emergency identity is described in C03. |
| OAuth apps | App consent is logged. Users may grant unreviewed apps read-only file access. |
| Domain-wide delegation | An approved nightly reporting integration has read-only Drive scope. Data Operations owns it; the last scope review was two years ago. It is a business dependency. |
| External sharing | The top-level setting restricts sharing to approved partner domains. The export omits child organizational-unit overrides and individual file permissions. |
| Sensitive-file controls | An automated review reports “unable to determine” from available administrative events. Direct configuration evidence is not attached. |
| Email authentication | SPF and DKIM are configured. DMARC is in monitoring mode; legitimate sender inventory and report-review evidence are not supplied. |

### Exhibit D — Delivery, logging and recovery

**C09 · Delivery and operating controls**

| Area | Evidence supplied |
| --- | --- |
| Build checks | Application changes require review and dependency checks before a release build. |
| Deployment identity | CI uses a service-account key stored in repository secrets. Its role is shown in C02; workload federation is not configured. |
| Release selection | Approval records a build ID. Deployment fetches the package labelled `approved`; that label can be reassigned. The deployment step does not compare the downloaded package with the approved build. |
| Runtime secrets | The API and a reporting worker have separate identities. Both can read a single secret containing the API's database credential and the worker's report-service credential. The owner confirms each workload needs only its own credential. Both load credentials at startup; rotation-test evidence is absent. |
| Operating controls | The application uses TLS and the database is encrypted at rest. A current operating-system patch-status report is not included. |

**C10 · Audit and recovery evidence**

| Area | Evidence supplied |
| --- | --- |
| Audit coverage | Administrative and WAF request logs are enabled. Storage object-read auditing is not enabled. |
| Export | Collaboration audit events are exported daily. No recent collection-health test is supplied. The excerpt covers only selected events. |
| Evidence protection | The audit archive is inside the production administrative boundary; production administrators can delete it. |
| Response ownership | An operations contact is assigned. No record traces a test event from collection through rule matching, notification delivery and responder acknowledgement. |
| Backups | Encrypted backups run daily. The latest job succeeded. The last documented restoration exercise was six months ago; current recovery objectives are not supplied. |

### Exhibit E — Cloud portability

**C11 · Hypothetical design change**

Assume the customer API and its deployment pipeline will be hosted on another major cloud provider. The workforce identity source and collaboration suite remain unchanged for this exercise. Both environments will run during a short transition, and customer data may change after traffic moves.

No target services have been selected. Recommend three security outcomes to preserve, the evidence needed before cutover, and a rollback approach. State any target-provider capabilities that must be verified rather than assuming that similarly named products behave identically.

## 5. Event timeline

The excerpt ends at 09:35 UTC. Configuration, group changes and active sessions may not update simultaneously. Request IDs connect records; an asserted identity or an HTTP status alone does not establish caller attribution or the exact content returned.

| ID | UTC | Source | Recorded event |
| --- | --- | --- | --- |
| E01 | 08:55:00 | Change ticket | CHG-71 approves staging access for Contractor C. |
| E02 | 09:00:00 | Group audit | Support lead adds Contractor C to contractors, referencing CHG-71. |
| E03 | 09:06:00 | Cloud audit | Contractor C changes metadata on production worker-2. Result: success. Authorization: Compute Admin. No PAM grant ID is included in this export. |
| E04 | 09:10:01 | WAF | req-101: POST `/checkout`; field customer_name includes `O'Neil`; priority 200; signature sqli-example-01; deny; HTTP 403. Complete request body omitted. |
| E05 | 09:10:30 | Support ticket | Support S reports req-101 rejected a legitimate customer order. |
| E06 | 09:11:00 | WAF | req-102: POST `/api/orders`; priority 100; allow; HTTP 200. Request body omitted. |
| E07 | 09:13:00 | Collaboration access | Support S accesses files from the office network. Managed device: no; encryption: no; screen lock: no. Decision: allow. |
| E08 | 09:15:00 | Staff application | req-201 arrives directly at backend HTTPS. IAP token absent; header identity Support S; `/customers/customer-22`; HTTP 200; 2,400 response bytes. Response body omitted. |
| E09 | 09:16:00 | IAP | req-202: Support S allowed through staff-lb. No device posture is included in the record. |
| E10 | 09:16:01 | Staff application | req-202: Support S belongs to team-A and requests team-B's customer-23. HTTP 200. Object-level authorization result and response body are absent. |
| E11 | 09:18:00 | OAuth audit | Support S authorizes notes-helper-19 for read-only Drive access. Review status: not reviewed. |
| E12 | 09:21:00 | Security ticket | INC-44 reports the contractor's production change and requests access removal. |
| E13 | 09:24:00 | Group audit | Support lead removes Contractor C from contractors. |
| E14 | 09:26:00 | Cloud audit | Contractor C changes production worker-2 metadata again. Result: success. No PAM grant ID is included. |
| E15 | 09:28:00 | Cloud audit | ci-deploy changes customer-exports bucket configuration successfully; deployment job references CHG-72. The referenced approval is not supplied. |
| E16 | 09:30:00 | Analyst note | No object-read events are present. Relevant auditing is disabled. Full network and session histories are unavailable in this export. |
| E17 | 09:32:00 | Support ticket | Manager states req-201 does not correspond to an approved support task. No additional evidence of caller identity or returned data is attached. |
| E18 | 09:35:00 | Operations | Last night's backup job succeeded. No new restoration test has been performed. Application traffic remains within its normal operating range. |

## 6. Prepare your recommendation

Return to the [five tasks](README.md#your-submission). Reference the evidence and requirements behind your decisions. Be clear about what you know, what you need to check, and what you would do first.
