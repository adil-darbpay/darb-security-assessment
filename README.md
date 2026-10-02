# Darb security engineering assessment

For mid-level security engineer candidates. Assessment version 2.0.

We would like you to review a service, recommend two security changes, and talk us through your decisions. We want to see how you use evidence, decide what matters most, and keep the service working while you improve its security. The final part asks you to explain a security issue to colleagues outside the security team.

| Preparation | Live discussion | Submission |
| --- | --- | --- |
| Maximum 2 hours of active work | 90 minutes | Up to four pages, using the five headings below |

**You do not need a cloud account, paid software, a scanner or a development environment.** Read the case in GitHub and answer in plain text or a document. There is nothing to install, deploy or run, and no need to clone the repository.

## Start here

1. Read [the case study](CASE.md), including the requirements and evidence exhibits.
2. Complete the five deliverables below. Use evidence IDs rather than copying the case into your answer.
3. Stop at two hours. Note assumptions, missing evidence and anything you did not finish.
4. Submit privately through the channel in your hiring invitation. Ask the recruiter if the channel is missing; do not publish answers or personal details in issues, pull requests or forks.

The case does not describe Darb's systems or staff. Use the rules supplied in the case, and explain any assumptions. You can answer in general security terms; exact product names, configuration syntax and polished diagrams are not required.

## Your submission

Include your name or candidate ID, the time you spent, and the sources or assistance you used. Use the five headings below. Tables and simple sketches are welcome.

### 1. Prioritized risk assessment

Select **five findings** across identity/access, application/network protection, and workforce or data security. You do not need to identify every issue.

For each finding, provide:

- The evidence ID and requirement involved.
- The access or failure path and likely business impact.
- A priority, with a short reason.
- Separately, the evidence status: **confirmed**, **plausible**, or **needs verification**.
- The key missing fact, if any, that could change your decision.

Choose one of your five findings that you would address after the others. Tell us why it can wait and what would change that decision.

### 2. Two proposed security changes

Choose **one change to identity or privileged access** and **one change to WAF, network or application access**. For each, cover these six points:

1. Current behavior and the intended security outcome.
2. The smallest useful change, affected users/traffic and owner.
3. A legitimate action that must succeed, and the evidence of success.
4. An unwanted action that must fail, and the evidence of denial.
5. Dependencies, rollout and a measurable stop or rollback condition.
6. Remaining exposure and an alternative you considered.

Describe expected results. Do not run commands, deploy resources or generate attack traffic. Respect the case's service-availability and recovery requirements.

### 3. Initial incident assessment

- Build a **four-event timeline** using supplied IDs. Separate observations from explanations you still need to verify; the events need not have a common actor.
- State your **first three response actions**, in order, including why, who should act and what evidence to preserve or obtain.
- Describe **one detection**: data source and fields, condition or correlation, a legitimate explanation, a validation case and response owner. Plain language is sufficient.

If you cannot tell whether an account was compromised or data was accessed, say so and identify the evidence you need.

### 4. Cloud portability review

Use C11 to select **three security outcomes that must survive a hypothetical provider change**. For each, give a control approach and an acceptance test. State one cutover stop condition and one rollback or data-reconciliation concern.

Explain which permissions, trust decisions and logs must stay in place, and which differences in the target provider could change the result.

### 5. Staff awareness exercise

Prepare a **five-minute explanation for support and operations colleagues**, using up to 150 words of speaking notes or one simple slide. Pick one behavior from the case, such as how to handle an unexpected sign-in or app-consent request.

Tell them what to do, how to verify or report the request, and what to do if they have already approved it. Use `security-help@example.com` as the fictional reporting channel. Finish with one question to check understanding and how you would check a few weeks later whether staff act correctly. You will present this to the interviewers; no employee messages or simulations are part of the exercise.

**Suggested preparation:** 20 minutes reading, 30 on findings, 30 on changes, 25 on incident/portability, and 15 on awareness and your assistance declaration.

## Resources and AI use

**Preparation:** you may use documentation and AI assistance. Tell us what you used, where it helped, and how you checked the result; write `None` if you did not use AI. Be ready to explain your submission. Do not have another person prepare your answers.

**Live discussion:** turn off AI chat, generated search answers and coding assistants. You may use your notes, an ordinary editor and reference documentation. Tell the interviewer what you are consulting. We can provide reference material if needed.

Expect to explain a finding in your own words, discuss an alternative you rejected, and adjust your proposal when new information is introduced. We assess your understanding in that discussion; we do not use AI-writing detectors or inspect private devices.

## Live discussion

We use the same core exercises, time allowance and scoring criteria for each candidate in a hiring round. Follow-up questions ask you to explain or check your reasoning.

| Minutes | Exercise | What you will demonstrate |
| --- | --- | --- |
| 0–5 | Setup | Confirm scope, resources and priorities. |
| 5–15 | Defend your submission | Reconstruct one finding, its evidence and the reason for its priority. |
| 15–35 | Identity and workforce access | Trace a principal through groups and permissions; respond to changed access/device evidence. |
| 35–50 | Safe control change | Predict permitted and denied requests; revise a change after an operational constraint. |
| 50–65 | Incident and operations | Distinguish facts from hypotheses, choose response actions, and explain record-level authorization and detection/recovery tests. |
| 65–75 | Cloud portability | Preserve security outcomes when implementation details change. |
| 75–85 | Awareness | Deliver your five-minute lesson and respond to staff questions. |
| 85–90 | Wrap-up | Clarifications and candidate questions. |

Arrange accessibility or connectivity accommodations with the recruiter before the session. If a technical problem prevents an exercise, an equivalent opportunity should be provided rather than silently treating missing evidence as failure.

## How your work is evaluated

The assessment is scored out of **100: 20 points for preparation and 80 for observed live work**. Prepared work is scored by deliverable, so selecting five findings does not create an expectation to write about every security domain.

| Prepared deliverable | Weight |
| --- | ---: |
| Risk assessment and prioritization | 6 |
| Two change proposals | 6 |
| Incident reasoning and detection | 4 |
| Cloud portability | 2 |
| Awareness preparation | 2 |
| **Prepared total** | **20** |

| Live competency | Weight | Expected mid-level evidence |
| --- | ---: | --- |
| Identity, groups and privileged access | 16 | Explains effective permissions, scoped elevation and how to verify access changes. |
| WAF, network and application ingress | 12 | Traces the enforcement path and preserves legitimate traffic while closing unwanted access. |
| Workspace and device context | 12 | Reasons about policy scope, device conditions, administrative access and app grants. |
| Incident assessment and detection | 12 | Separates fact from inference, chooses proportionate actions and specifies useful detection evidence. |
| Internal application, data and operations | 8 | Distinguishes authentication from authorization and considers workload identity, data boundaries and recoverability. |
| Cloud portability | 8 | Defines outcomes and tests rather than assuming products are equivalent. |
| Staff awareness | 8 | Teaches a concrete behavior, handles mistakes constructively and checks understanding. |
| Communication and prioritization | 4 | Makes a clear recommendation, states uncertainty and updates it when evidence changes. |
| **Live total** | **80** | |

Each row receives a **0–4 rating**. Its points equal `weight × rating ÷ 4`.

| Rating | Observable performance |
| --- | --- |
| 0 | No usable evidence, or a material unsafe misunderstanding remains after a neutral clarification. |
| 1 | Names a control or concern but cannot explain the relevant path or effect. |
| 2 | Explains part of the problem and a plausible response; needs substantial help with validation or operational consequences. |
| 3 | Meets the mid-level standard: independently links evidence to impact and proposes a proportionate, verifiable response at the requested depth. |
| 4 | Meets 3 and weighs alternatives, remaining exposure and operational tradeoffs. During live work, adjusts the recommendation when evidence changes. |

You can look up a product detail, acknowledge a gap or correct an earlier answer. Explain why your conclusion changed. Extra findings and presentation polish do not earn extra points on their own.

<details>
<summary>Consistent evaluation and hiring decisions</summary>

Use the same case version, core prompts, additional facts and time allowances within a round. Agree any live changes before the round begins. Ask comparable probes such as “show the access path,” “what would disprove your conclusion?” and “which legitimate action must still work?” Do not improvise unrelated puzzles for different candidates.

Where possible, two assessors record evidence and score independently before discussing a recommendation. Record the observed behavior, relevant IDs and substantive hints. Mark an exercise that could not be observed as unassessed and resolve it before making a score-based recommendation. Do not double-penalize the same issue across competencies without distinct evidence.

Score the risk argument, not a setting in isolation. A credential's age, an access duration or a monitoring-mode policy needs context: the permitted action, business requirement, existing controls and evidence of effectiveness.

**Provisional benchmark:** at least 70/100 overall, at least 52/80 live, and a live rating of at least 3 in identity/privileged access, WAF/network protection and incident assessment. A total of at least 85 meeting these conditions supports a stronger recommendation. A high total does not erase a persistent material safety error; review the exact reasoning and any subsequent correction.

These are proposed review criteria, not validated predictors of job performance or an automatic hiring decision. Before using them, pilot the exercise with people familiar with comparable mid-level work, check the two-hour workload and reconcile assessor differences. Calibrate before the hiring round and keep the agreed criteria stable throughout it. Combine this evidence with relevant experience and the rest of the hiring process.

</details>

## Boundaries

Use only the fictional evidence. Do not scan real systems or submit production data, secrets or credentials. This is a reasoning exercise, not an instruction to perform security changes. Candidate submissions and completed evaluations must remain outside the public repository.

<details>
<summary>Research basis and optional references</summary>

Reviewed 2 October 2026. The design uses representative work tasks, observable behavior and consistent evaluation. The following sources informed specific design choices; they do not certify this assessment or mandate its timing and scoring.

| Source | Application to this assessment |
| --- | --- |
| [OPM work samples](https://www.opm.gov/policy-data-oversight/assessment-and-selection/other-assessment-methods/work-samples-and-simulations/) and [structured interviews](https://www.opm.gov/policy-data-oversight/assessment-and-selection/structured-interviews/) | Use realistic job activities, common prompts and explicit rating standards. |
| [NIST NICE Framework](https://www.nist.gov/itl/applied-cybersecurity/nice/nice-framework-resource-center/getting-started) | Assess tasks and demonstrated skills rather than a list of product names. |
| [NIST SP 800-61 Rev. 3](https://csrc.nist.gov/pubs/sp/800/61/r3/final) | Connect incident decisions to preparation, response and recovery outcomes. |
| [NIST SP 800-50 Rev. 1](https://csrc.nist.gov/pubs/sp/800/50/r1/final) | Evaluate an audience-specific lesson, behavior and follow-up rather than attendance alone. |
| [OWASP ASVS 5.0.0 authorization requirements](https://github.com/OWASP/ASVS/blob/v5.0.0/5.0/en/0x17-V8-Authorization.md) | Require evidence of application authorization, not only successful login. |
| [ScubaGoggles limitations](https://github.com/cisagov/ScubaGoggles/blob/main/docs/usage/Limitations.md) | Include evidence gaps and uncertain review results that require manual validation. |
| [OWASP CRS rules](https://github.com/coreruleset/coreruleset), [tuning guidance](https://coreruleset.org/docs/2-how-crs-works/2-3-false-positives-and-tuning/) and [go-ftw test format](https://github.com/coreruleset/go-ftw#test-file-format) | Ask for legitimate and unwanted request outcomes and safe tuning. |
| [Sigma cloud rule example](https://github.com/SigmaHQ/sigma/blob/master/rules/cloud/gcp/audit/gcp_firewall_rule_modified_or_deleted.yml) | Assess data requirements, expected administrative activity and detection validation. |

Technical references include [Google IAM propagation](https://docs.cloud.google.com/iam/docs/access-change-propagation), [IAP identity verification](https://docs.cloud.google.com/iap/docs/signed-headers-howto), [Workspace context-aware access](https://knowledge.workspace.google.com/admin/security/protect-your-business-with-context-aware-access), [AWS identity practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) and [Azure privileged access](https://learn.microsoft.com/en-us/entra/id-governance/privileged-identity-management/pim-configure). Check actual provider capabilities before real-world use.

The broader research also reviewed [GAM](https://github.com/GAM-team/GAM), [Cloud Foundation Fabric](https://github.com/GoogleCloudPlatform/cloud-foundation-fabric), [Terraform Example Foundation](https://github.com/terraform-google-modules/terraform-example-foundation), [JIT Groups](https://github.com/GoogleCloudPlatform/jit-groups), [Prowler](https://github.com/prowler-cloud/prowler), [OpenSSF Scorecard](https://github.com/ossf/scorecard), [Threat Dragon](https://github.com/OWASP/threat-dragon), [ScoutSuite](https://github.com/nccgroup/ScoutSuite), [gcp_scanner](https://github.com/google/gcp_scanner), [Stratus Red Team](https://github.com/DataDog/stratus-red-team), [Juice Shop](https://github.com/juice-shop/juice-shop), [Pacu](https://github.com/RhinoSecurityLabs/pacu) and the archived [Forseti](https://github.com/forseti-security/forseti-security) project. These are background references, not candidate dependencies or recommended live exercises.

The research covered primary guidance, repository documentation and selected examples. It was not a source-code audit, and no security tool was run against Darb. Reading these references is optional; the case contains the information needed for the exercise.

</details>
