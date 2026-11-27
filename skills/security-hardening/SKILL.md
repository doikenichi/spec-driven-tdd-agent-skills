---
name: security-hardening
description: Apply secure-by-default practices across the repo, code, dependencies, supply chain, infrastructure and runtime — GitHub rulesets, SECURITY.md, secret scanning, Dependabot, CodeQL/Semgrep SAST, SBOM and signing, IaC and container scanning, DAST, STRIDE threat modeling, OWASP Top 10 (web, API, LLM). Use when setting up a repo or pipeline, adding a dependency, endpoint, container, IaC or agent tool, or when asked about security.
---

# Security hardening

Security is shift-left, automated, and part of the definition of done. Map the controls to recognised standards: OWASP ASVS, the OWASP Top 10 (web, API and LLM applications), the NIST SSDF, the OpenSSF Scorecard and SLSA.

## 1. Repository
- **Rulesets on the default branch:** changes only through PRs; block force pushes and deletion; require status checks once CI exists; require conversation resolution; linear history (squash merge).
- **Settings:** squash merge only, delete head branches after merge, disable unused features (wiki, projects).
- `CODEOWNERS`, PR template (with a security checklist), issue templates.
- `SECURITY.md` (supported versions, how to report); enable **private vulnerability reporting**.
- **Secret scanning and push protection** on (the default for public repos); add custom patterns where useful.

## 2. Code (SAST)
- **CodeQL** for Python and TypeScript (free for public repos). Use the `security-extended` queries.
- **Semgrep** in CI with OWASP and language rulesets. Fail the build on high or critical findings, and triage the rest.
- Linters with security rules: ruff (`S`, the bandit rules) and eslint security plugins.
- Fix findings with a test first (TDD): write a test that reproduces the vulnerable behaviour.

## 3. Dependencies (SCA)
- **Dependabot** alerts and security updates; `.github/dependabot.yml` for `uv`, `npm`/`pnpm`, `docker` and `github-actions`, grouped and weekly.
- Lockfiles committed. Pin versions, and pin CI actions and orbs by digest or SHA.
- License policy check (allow-list of OSI-approved licenses).

## 4. Supply chain
- SBOMs (CycloneDX or SPDX) generated in CI for every image.
- Images signed and attested with **cosign** (keyless); aim for SLSA build provenance.
- An **OpenSSF Scorecard** run, with the badge in the README.

## 5. Containers and IaC
- Minimal, non-root images, read-only filesystems, no secrets in layers, health checks; lint with **hadolint**.
- **Trivy** scans for images, filesystems and IaC. **Checkov** (or Trivy) for Terraform, Kubernetes and Helm.
- Kubernetes: `securityContext` (runAsNonRoot, drop all capabilities, seccomp RuntimeDefault), NetworkPolicies, resource limits, Pod Security Standards set to "restricted".

## 6. Runtime and application
- Input validation at every boundary (Pydantic, zod), RFC 9457 errors with no stack traces, rate limiting, CORS allow-list, security headers.
- Least-privilege credentials per service. Secrets come from the platform's secret store, never from git.
- **DAST:** an OWASP ZAP baseline scan against preview and production URLs in CI.
- **LLM and agents:** the OWASP Top 10 for LLM applications: prompt-injection defences, output handling, tool allow-lists, least-privilege tools, spend and rate limits, and no secrets in prompts or logs (see `agent-testing` for the red-team tests).

## 7. Threat modeling
- A lightweight **STRIDE** model per component or data flow, in the arc42 "Risks" section (or `docs/security/threat-model.md`), with a data-flow diagram (see `uml-and-c4-diagrams`).
- Every high risk is mapped to a control and a test.

## Checklist for any change
- [ ] New input validated and tested, including malicious cases
- [ ] No new secret; config comes from env or the secret store
- [ ] New dependency: current, maintained, license OK, pinned
- [ ] Scans green (CodeQL, Semgrep, Trivy, gitleaks)
- [ ] Threat model or ADR updated if the attack surface changed
