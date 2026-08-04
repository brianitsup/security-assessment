# Assessment methodology

Eight assessment areas. Apply the ones that fit the project and skip the ones that don't — but say *why* you skipped them, so the reader knows it was a decision, not an oversight. Depth scales with the project: a small solo SaaS gets a focused pass on the areas that matter (usually appsec, source review, dependencies, secrets/config, and multi-tenant isolation); a large platform warrants all eight in depth.

## 1. Threat modeling (STRIDE)

Build a project-specific threat model. Identify: assets worth protecting, users and system actors, entry points, trust boundaries, data flows, privileged operations, likely threat actors, abuse cases, attack paths, security assumptions, existing controls, missing controls, and residual risks.

For each relevant component, consider the STRIDE categories:

- **S**poofing — impersonating a user, service, or system
- **T**ampering — modifying data or code in transit or at rest
- **R**epudiation — acting without an attributable, non-forgeable trail
- **I**nformation disclosure — exposing data to those who shouldn't see it
- **D**enial of service — degrading or removing availability
- **E**levation of privilege — gaining rights beyond what's granted

Do **not** force every category onto every component. Apply only the ones that are relevant and explain why they apply. A stateless read-only endpoint has a very different threat profile from a payment webhook or an admin role-change action.

## 2. Application security review (OWASP)

Assess applicable risks using current OWASP guidance — the OWASP Top 10, API Security Top 10, ASVS, and Cheat Sheet Series. Cover, where relevant:

- Authentication weaknesses; session management
- Authorization / access-control failures (including IDOR and broken object-level authorization)
- Injection (SQL, NoSQL, command, LDAP, template)
- Cross-site scripting (XSS); cross-site request forgery (CSRF)
- Server-side request forgery (SSRF); open redirects
- Insecure deserialization; path traversal; unrestricted file upload
- Race conditions and TOCTOU issues
- Cryptographic failures; sensitive-data exposure
- Security misconfiguration; vulnerable/outdated components
- Insufficient logging and monitoring
- API weaknesses: excessive data exposure, mass assignment, missing rate limiting, broken function-level authorization
- Business-logic vulnerabilities (the ones scanners miss — abuse of legitimate flows)
- Unsafe error handling (stack traces, internal detail leakage)

Anchor each finding to a specific location and behavior, not just a category name.

## 3. Adversarial assessment

Think like an attacker, stay strictly in scope, and develop hypotheses *before* testing them. Consider realistic attack paths through: public interfaces, APIs, authentication, password reset, account registration, session handling, role changes, admin features, file uploads, search/filtering, webhooks, background jobs, payment flows, multi-tenant isolation, mobile clients, cloud configuration, CI/CD, source repositories, dependency chains, secrets, logging systems, developer tooling, internal services, and misconfigured environments.

For **every** hypothesis, record:

- The suspected weakness
- Why it may exist
- The evidence inspected
- The test performed
- The result
- Whether the issue was confirmed
- Any limitations
- Recommended next steps

Never present an unsuccessful or incomplete test as a confirmed vulnerability. "I suspected X, tested Y, and could not confirm it" is a legitimate, valuable result.

## 4. Source-code review

Where source is available, focus on the security-sensitive areas rather than reading everything uniformly: authentication, authorization, input validation, output encoding, database queries, file operations, cryptographic operations, secret handling, token generation/validation, session management, error handling, logging, dependency usage, deserialization, network requests, webhook validation, administrative actions, multi-tenant data access, permission checks, infrastructure config, and CI/CD workflows.

**Trace untrusted input to sensitive sinks.** Follow a value from where it enters (request, upload, webhook, third-party response) to where it does something dangerous (query, exec, write, render, auth decision). That trace is what turns a suspicion into a confirmed finding.

Do not rely solely on keyword searches or automated scanners — validate each candidate against the surrounding code, execution flow, configuration, and application context. A `dangerouslySetInnerHTML` or a string-concatenated query is a lead, not a verdict.

## 5. Dependency and supply-chain assessment

Identify dependencies from the relevant manifests and lockfiles: `package.json` + lockfile, `requirements.txt` / `pyproject.toml` / `Pipfile`, `Gemfile`, `go.mod`, `Cargo.toml`, Maven/Gradle, NuGet, container images, GitHub Actions, CI/CD plugins, and IaC modules.

For each relevant dependency:

- Determine the installed version (from the lockfile, not the range in the manifest)
- Confirm whether it's a direct or transitive dependency
- Search current authoritative sources for known vulnerabilities
- Check official security advisories and vendor release notes
- Check whether the version is still supported / not EOL
- **Determine whether the vulnerable feature or code path is actually used** — a version match alone is not proof of exploitability
- Identify available patches or mitigations

Prefer authoritative sources: official vendor docs, official security advisories, GitHub Security Advisories, NIST NVD, MITRE CVE/CWE, package-maintainer advisories, framework security announcements, cloud-provider security docs. Use web search to verify current versions, advisories, patches, and EOL status. **Record the source and access date** for anything time-sensitive.

## 6. Secrets and configuration review

Inspect for: hard-coded credentials, API keys, private keys, tokens, connection strings, sensitive env vars, secrets committed to version control (check history, not just the working tree), insecure example credentials, publicly exposed configuration, weak defaults, debug mode left on, overly permissive CORS, insecure cookies (missing `HttpOnly`/`Secure`/`SameSite`), missing security headers, unsafe storage permissions, public cloud resources, excessive IAM privileges, and unprotected admin interfaces.

**Never reveal full secret values** in the report. Mask them (e.g. `sk_live_…a1b2`) and explain how to revoke, rotate, remove from history, and prevent recurrence. A leaked secret is "assume compromised" — rotation is not optional even if the repo is private.

## 7. Infrastructure and deployment review

Where applicable: cloud configuration, network exposure, firewall rules, IAM permissions, container security, Kubernetes config, server hardening, TLS configuration, domain/DNS security, object-storage permissions, database exposure, backup security, CI/CD permissions, artifact integrity, environment separation, production access, secret injection, logging/monitoring, patch management, and disaster recovery.

Clearly distinguish three things — conflating them produces false confidence:

- **What's visible in the repository** (IaC, config files)
- **What's confirmed in the deployed environment** (only if you have authorized access to check)
- **What can't be verified without additional access**

## 8. Privacy and data protection

Identify the sensitive data the system handles: personal information, credentials, financial data, health data, location data, confidential business information, uploaded documents, audit logs, analytics, and third-party data sharing.

Review: data collection, minimization, consent, storage, encryption (at rest and in transit), retention, deletion, access controls, logging, backups, third-party processors, cross-border transfers, and incident response.

Do **not** make legal-compliance claims unless the applicable jurisdiction and requirements are known. Label anything legal/regulatory as needing professional review — you can flag a data-protection *risk* without asserting a *violation* of a specific law.
