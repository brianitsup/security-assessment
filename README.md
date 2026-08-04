# security-assessment

A reusable [Claude skill](https://docs.claude.com) for conducting **authorized, evidence-driven security assessments** of software projects — from an adversarial (attacker's-eye) angle and a structured-review angle (OWASP, STRIDE, CWE, NIST).

It identifies, verifies, prioritizes, and documents real weaknesses without inventing findings or overstating risk. Safety and authorization boundaries are enforced up front: assess only what you own or are explicitly authorized to test, non-destructive methods by default, and no active test that could affect production, data, or availability without explicit approval.

## What's inside

| File | Purpose |
|------|---------|
| `SKILL.md` | Core workflow — authorization gate, assessment phases, fact/hypothesis separation, verification discipline. Always loaded when the skill triggers. |
| `references/discovery.md` | Initial project discovery checklist and how to classify what you learn. |
| `references/methodology.md` | The eight assessment areas: threat modeling, appsec review, adversarial testing, source review, dependency/supply-chain, secrets/config, infrastructure, privacy. |
| `references/findings-and-reporting.md` | Finding schema, severity/prioritization, the six deliverables, and a finding template. |

## Deliverables it produces

Executive security report · technical findings report · threat model · security requirements specification · phased remediation plan · assessment record.

## Install

**Claude Code (local):** copy this folder into your skills directory —

```bash
# personal (all projects)
mkdir -p ~/.claude/skills && cp -r security-assessment ~/.claude/skills/

# or project-scoped
mkdir -p .claude/skills && cp -r security-assessment .claude/skills/
```

**Claude.ai:** upload the packaged `.skill` file and click **Save skill** on the file card.

## License

MIT © Brian Mangi
