# Security model and repository settings

## Assets and assumptions

Protect the integrity of infrastructure changes, policy decisions, and CI execution. Initial scope is accidental or deliberately introduced S3 configuration mistakes. Pull requests can alter executable code, workflow definitions, policies, or scanner settings.

A repository owner can change policies and repository settings. This lab does not prevent a malicious owner or administrator from bypassing their own controls.

## Planned controls

- Run PR checks without cloud credentials and with minimal read permissions.
- Use ephemeral hosted runners, pinned tool versions, and action commit SHAs.
- Do not apply Terraform in PR jobs.
- Never execute untrusted PR code with privileged pull_request_target credentials.
- Require an explicit allow decision; block missing inputs and failed prerequisites.
- Review workflow, policy, test, script, and scanner-configuration changes.
- Keep state, credentials and unsanitized plans out of Git.
- Treat synthetic fixtures separately from real plan-generation evidence.

## GitHub settings — pending

At the 19 October 2026 review, main was not protected. The current connector cannot write branch protection or rulesets.

After the actual workflow has run:
1. Add a rule targeting main.
2. Require pull requests and the exact final gate check name.
3. Require the branch to be current, or define and test the chosen merge strategy.
4. Restrict bypasses and document any administrator exceptions.
5. Where a separate reviewer is available, require review of security-control changes.
6. Demonstrate blocked merging for a failing PR, then a passing corrected PR.

CODEOWNERS identifies a maintainer but does not enforce review by itself. A solo owner cannot provide independent approval of their own PR; do not enable a review requirement that makes the lab unusable without documenting an appropriate collaborator process.

Do not describe the gate as enforced until the settings and blocked-merge experiment are recorded.
