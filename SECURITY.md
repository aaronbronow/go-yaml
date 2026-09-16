# Security Policy and Supply Chain Security

## Supported Versions

| Version | Supported          |
| ------- | ------------------ |
| v4      | :white_check_mark: |
| v3      | :white_check_mark: |
| < v3    | :x:                |

---

## Reporting a Vulnerability

If you discover a security vulnerability in `go-yaml`, please report it responsibly:
- **Email**: Contact the security team or maintainers at security@yaml.io or via GitHub Private Vulnerability Reporting.
- Please do not open public GitHub issues for security vulnerabilities until a patch and advisory have been prepared.

---

## SLSA Build Level 2 Compliance Checklist

This project aims to satisfy the requirements of the [SLSA (Supply-chain Levels for Software Artifacts) Build Level 2](https://slsa.dev/spec/v1.0/levels#build-levels) specification.

### 1. Automation Requirements (Technical & CI/CD Controls)

The following controls are enforced automatically in the build pipeline:

- [x] **Hosted Build Service**:
  - Builds execute exclusively on a dedicated, hosted build service (GitHub-hosted runners) rather than local developer workstations.
- [x] **Isolated Execution Environment**:
  - Every build runs inside an ephemeral, freshly provisioned container/runner instance without residual state from previous builds.
- [x] **Build as Code**:
  - Build recipes and procedures are version-controlled in the repository ([GNUmakefile](GNUmakefile) and [.github/workflows/go.yaml](.github/workflows/go.yaml)).
- [x] **Automated Artifact Hashing**:
  - Built binaries (`go-yaml`) are cryptographically digested using SHA-256 to anchor artifact identities.
- [x] **Automated SBOM Generation (Syft)**:
  - An SPDX-compliant Software Bill of Materials (`sbom.spdx.json`) is generated for every build using Syft.
- [x] **Provenance & Attestation Generation (Cosign / in-toto)**:
  - Cryptographic in-toto attestation predicates are generated tying the artifact hash to the source commit, workflow run, and SBOM.
- [x] **Verifiable Provenance Content**:
  - The attestation captures the source repository (`yaml/go-yaml`), checkout commit SHA, builder ID, build trigger, and input parameters.
- [x] **Non-Blocking Observability**:
  - Supply-chain steps (SBOM and attestation) produce inspectable audit logs in CI and fail soft during offline/dry-run states to avoid blocking emergency builds.

---

### 2. Process & Manual Requirements (Governance & Operational Controls)

The following controls require operational procedures, organization policy, and repository configuration:

- [ ] **Two-Party Review**:
  - Require at least one independent maintainer code review and approval before merging code into production branches (`main`).
  - *Documentation*: [GitHub Docs: Require pull request reviews before merging](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/defining-the-mergeability-of-pull-requests/about-protected-branches#require-pull-request-reviews-before-merging)
- [ ] **Branch Protection Rules**:
  - Enforce branch protection on `main`, `v3`, `v2`, `v1`:
    - Require pull requests before merging.
    - Dismiss stale pull request approvals when new commits are pushed.
    - Require all CI status checks (`test`, `test-cross-platform`, `lint`) to pass before merging.
    - Strictly prohibit force pushes (`git push --force`) and branch deletion.
  - *Documentation*: [GitHub Docs: Managing a branch protection rule](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/defining-the-mergeability-of-pull-requests/managing-a-branch-protection-rule)
- [ ] **Least Privilege Access & Secret Management**:
  - Secrets (e.g., `GHCR_TOKEN`) must have minimal scopes (`packages:write` only).
  - Prefer GitHub OIDC / Sigstore keyless authentication over long-lived static tokens where available.
  - Restrict repository administrative and release permissions to verified project maintainers.
  - *Documentation*: [GitHub Docs: Using secrets in GitHub Actions](https://docs.github.com/en/actions/security-for-github-actions/security-guides/using-secrets-in-github-actions) and [About security hardening with OpenID Connect](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/about-security-hardening-with-openid-connect)
- [ ] **Release Provenance Verification Procedure**:
  - Consumers and release managers must verify attestations before signing release tags or publishing distribution packages (`cosign verify-attestation` / `gh attestation verify`).
  - *Documentation*: [GitHub Docs: Using artifact attestations to establish provenance for builds](https://docs.github.com/en/actions/security-for-github-actions/using-artifact-attestations/using-artifact-attestations-to-establish-provenance-for-builds)
- [ ] **Vulnerability Management & Triage**:
  - Prompt triage of Dependabot security alerts and CVE notifications.
  - Documented incident response policy for compromised release signing keys or credentials.
  - *Documentation*: [GitHub Docs: About repository security advisories](https://docs.github.com/en/code-security/security-advisories/repository-security-advisories/about-repository-security-advisories) and [Configuring Dependabot security updates](https://docs.github.com/en/code-security/dependabot/dependabot-security-updates/configuring-dependabot-security-updates)
