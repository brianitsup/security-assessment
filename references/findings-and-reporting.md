# Findings, severity, and reporting

This file covers how to record a finding, how to rate it, and the deliverables to produce. The through-line is trust: a report the team can act on without re-verifying everything themselves.

## Finding schema

Every finding uses these fields:

- **Title** — specific and descriptive ("Unauthenticated access to `/api/admin/users`", not "Broken auth")
- **Status** — one of: Confirmed · Likely · Needs validation · Informational · Not reproducible · False positive · Accepted risk
- **Severity** — Critical · High · Medium · Low · Informational
- **Confidence** — how sure you are the finding is real and exploitable
- **Affected component** — the specific file/endpoint/service/dependency
- **Description** — what the weakness is
- **Evidence** — file paths, function/line references, config values, dependency versions, reproducible requests, sanitized responses, logs, test output, or authoritative references (mask secrets)
- **Reproduction / validation steps** — how to see it, or what's still needed to confirm it
- **Security impact** — what an attacker gains
- **Exploitation prerequisites** — what conditions/privileges are required
- **Likelihood** — how plausible exploitation is in practice
- **Recommended remediation** — the specific fix
- **Verification steps** — how the team confirms the fix worked
- **References** — CWE, OWASP, advisories, vendor docs (real ones only)

**Never invent** CVE IDs, versions, exploitability claims, test results, citations, code paths, configs, architecture, or compliance requirements. When evidence is incomplete, state explicitly what's unknown and what's required to confirm — that's a `Needs validation` finding, not a `Confirmed` one.

### Status semantics

- **Confirmed** — reproduced or proven from direct evidence
- **Likely** — strong evidence, exploitation near-certain but not demonstrated
- **Needs validation** — plausible, requires more access/info/testing to confirm
- **Informational** — not a vulnerability, but worth the team's awareness (hardening, hygiene)
- **Not reproducible** — investigated, couldn't reproduce
- **False positive** — a scanner/lead that turned out benign (record it so it isn't re-flagged)
- **Accepted risk** — real, but the owner has consciously chosen to accept it

## Severity and prioritization

Rate with a consistent method. Consider technical impact, business impact, exploitability, required privileges, user interaction, attack complexity, exposure, data sensitivity, number of affected users, existing controls, detectability, and remediation difficulty.

Use **CVSS where appropriate, but never rely on CVSS alone** — a CVSS 9.8 behind five controls in a system with no sensitive data may matter less than a CVSS 6 that exposes every tenant's records. Always state the **project-specific business impact separately** from any numeric score.

Priority buckets: **Critical · High · Medium · Low · Informational.**

Also classify each recommended action by *when* it should happen:

- **Immediate** — active or trivially exploitable, fix now
- **Before production** — must be resolved before/if going live
- **Next development cycle** — schedule into upcoming work
- **Planned improvement** — worth doing, not urgent
- **Defense in depth** — hardening beyond any single known issue

## Deliverables

Produce these, scaled to the engagement. For a small solo project a lightweight version of each is fine — don't generate 40 pages of boilerplate for a five-endpoint MVP. Match the artifact to the audience.

### 1. Executive security report
For owners and non-technical stakeholders. Include: executive summary, overall security posture, major strengths, critical risks, business impact, priority actions, assessment scope, methodology, limitations, and a recommended remediation roadmap. Plain language, no unexplained jargon.

### 2. Technical findings report
All confirmed and potential findings using the schema above: evidence, severity, confidence, reproduction steps, impact, remediation guidance, references, and retesting instructions. This is the engineers' working document.

### 3. Threat model
System overview, assets, actors, trust boundaries, data flows, threat scenarios, existing controls, recommended controls, and residual risks. (From `methodology.md` §1.)

### 4. Security requirements specification
Actionable, testable security requirements the project should adopt going forward, with acceptance criteria, covering: authentication, authorization, session, input validation, encryption, logging, secrets management, dependency management, infrastructure, CI/CD security, privacy, and incident response. This is what turns a one-off audit into durable practice.

### 5. Remediation plan
Organized into practical phases: immediate containment · critical fixes · pre-production requirements · short-term improvements · long-term improvements · retesting. For each action include: priority, owner/responsible role, estimated complexity, dependencies, and validation method.

### 6. Assessment record
The audit trail: files reviewed, tools used, commands executed, tests performed, sources consulted (with access dates), questions asked, assumptions made, limitations, and unverified areas. This lets someone reproduce or extend the work and makes the report's boundaries honest.

## Finding template

Use this shape for each entry in the technical findings report:

```markdown
### [FINDING-ID] Title
- **Status:** Confirmed | Likely | Needs validation | Informational | Not reproducible | False positive | Accepted risk
- **Severity:** Critical | High | Medium | Low | Informational   **Confidence:** High | Medium | Low
- **Action timing:** Immediate | Before production | Next cycle | Planned | Defense in depth
- **Affected component:** path/to/file.ts:120  ·  or endpoint/service/dependency

**Description**
What the weakness is.

**Evidence**
Concrete evidence — paths, lines, config values, versions, sanitized request/response, advisory links. Secrets masked.

**Impact**
What an attacker gains; who/what is affected.

**Exploitation prerequisites & likelihood**
Required privileges, conditions, and how plausible exploitation is.

**Reproduction / validation**
Steps to observe it, or what's still needed to confirm it.

**Remediation**
The specific fix, with a code or config example where it helps.

**Verification**
How to confirm the fix works (retest steps).

**References**
CWE-XXX, OWASP …, advisory/vendor links (real only).
```

## Closing discipline

- Prefer verified findings over a large volume of speculative warnings.
- Explain uncertainty clearly rather than hiding it behind confident phrasing.
- Never state or imply that no discovered vulnerabilities means the system is secure — say what was and wasn't covered.
- Recommend which controls should become part of the team's ongoing development workflow, so the next release doesn't reintroduce what this assessment just fixed.
