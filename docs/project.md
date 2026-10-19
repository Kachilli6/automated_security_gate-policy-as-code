# DevSecOps Automated Security Gate and Policy as Code Lab

Project proposal and first milestone documentation
12 week university project | Prepared 19 October 2026

This project will build and evaluate a pull request security gate for AWS S3 infrastructure written in Terraform. Checkov will check selected configuration rules, while Open Policy Agent will enforce custom requirements against Terraform plan JSON. The recommended scope is a reproducible, testable lab with no automatic cloud deployment.

## 1 Project topic

Design and evaluation of an automated pull request security gate using Terraform, Checkov, Open Policy Agent and GitHub Actions for AWS S3 configurations.

## 2 Goal

By week 12, demonstrate that the gate permits configurations meeting the documented lab policy and blocks known policy violations before merge. Deliver the repository, tested policies, reproducible examples, evidence of merge enforcement and an evaluation report.

## 3 Tasks

Define the threat model and policy scope; study official documentation; create compliant and noncompliant Terraform examples; establish repeatable plan generation; implement and test Rego rules; integrate checks into GitHub Actions; configure merge protection; evaluate detection and failure handling; maintain evidence and a final portfolio demonstration.

## 4 Relevance and research question

Infrastructure code can repeatedly introduce the same configuration mistake. Checking changes during review gives developers earlier feedback and makes requirements versioned and testable. This lab studies whether built-in IaC scanning plus custom policy can enforce a small, explicit S3 standard reliably. Shift-left complements deployment and runtime controls; security was not historically performed only after deployment. [1, 2]

## Scope and success boundary

Core scope: one AWS service, one cloud provider, root-module Terraform examples and 3–4 custom controls. Kubernetes, application SAST/DAST, multi-cloud support and production deployment are excluded. Passing the gate demonstrates compliance with the selected lab rules, not complete security or CIS certification.

## 5 Topic analysis and source findings

The following primary sources were reviewed for this design. Reference numbers map to the bibliography. The findings are design inputs; implementation results remain to be collected.

## Secure development lifecycle

NIST SSDF describes integrating security practices into the development lifecycle. It supports treating security requirements, verification and documented outcomes as routine engineering work. The lab uses this approach but does not claim that one CI gate implements the entire SSDF. [1]

## Terraform plans and policy input

HashiCorp distinguishes configuration, planned values and resource changes. Terraform creates a saved plan; terraform show -json exports it. This JSON is not merely an HCL abstract syntax tree. Unknown values and deletion actions require explicit handling. The lab must reject unsupported input and avoid interpreting absent security information as a pass. [3]

## Built in scanning and custom policy

Checkov provides selectable checks, output formats and hard/soft failure options. The lab will record exact check IDs and their purpose in a policy catalogue. Calling every Checkov check a CIS rule is inaccurate; any CIS mapping must identify a benchmark version and control explicitly. OPA provides programmable decisions over Terraform data, enabling requirements specific to this lab. [2, 4]

## Policy decisions and process exit codes

OPA evaluation success does not itself mean that a configuration is allowed. A defined Boolean false is still an evaluation result. The wrapper must require an explicit true decision, distinguish violations from execution errors, and test undefined decisions. Use Rego v1 syntax consistently with the chosen OPA version. [5, 6]

## Merge protection and pipeline trust

GitHub requires repository configuration to make checks a merge prerequisite. Failed jobs alone do not enforce this. Required checks may accept skipped or neutral conclusions, so the proposed final gate explicitly verifies that all mandatory jobs succeeded. Protect workflow and policy changes through review as well. [7, 8]

## S3 encryption baseline

AWS automatically applies SSE-S3 to new object uploads. Therefore, missing explicit encryption configuration is not proof that newly uploaded objects are unencrypted. A useful custom rule is an explicit organizational requirement for SSE-KMS, or explicit declaration of an approved algorithm. This distinction must appear in the report. [9]

## 6 Methods tools and corrected architecture

Method: controlled experiments using known compliant and violating examples. Compare expected outcomes with Checkov findings, OPA decisions and the final GitHub result. Keep the control set and tool versions fixed during each comparison.

| Stage | Responsibility |
| --- | --- |
| Pull request | Run on pull_request using an ephemeral hosted runner and minimal permissions. |
| Validation | Check formatting and validate Terraform after initialization. |
| Checkov | Scan the intended Terraform directory using the documented blocking checks. |
| Plan and OPA | Generate a saved plan, export JSON, validate input and evaluate custom policy. |
| Policy tests | Run positive, negative and malformed-input tests; fail if no tests execute. |
| Final security gate | Run even after upstream failure; require every mandatory job to succeed. |
| Merge enforcement | Make the final gate a required status check on main; test with a failing PR. |

Checkov and the plan/policy path can run independently so both produce feedback. A final aggregation job uses an always-run condition and explicit dependency-result checks. A successful gate makes a PR eligible for merge; it does not merge or deploy automatically.

## Plan generation contract

```text
terraform -chdir=configs init -backend=false
terraform -chdir=configs validate
terraform -chdir=configs plan -out=tfplan
terraform -chdir=configs show -json tfplan > tfplan.json
```

These commands describe the intended flow, not a verified setup. AWS provider configuration and credentials must be resolved first. During the catch-up milestone, use clearly labelled synthetic plan fixtures for OPA tests. By week 5, demonstrate a real Terraform-generated plan. A credential-free, empty-state example with provider validation calls disabled is an option to verify; -refresh=false alone does not guarantee no AWS calls. Avoid data sources in this mode.

## Execution and trust controls

Do not run terraform apply in PR jobs. Use no cloud secrets in the initial pipeline. If real AWS access is later needed, use a separate trusted workflow with scoped OIDC access. Do not execute untrusted PR code with pull_request_target credentials. Pin dependencies and action commit SHAs. Protect changes to workflows, policies and scanner configuration with review. [8]

## Repository structure and policy design

Keep the original documentation filenames, but group detailed notes under docs. Separate the normal target from expected-failure examples. This is the target layout. The organization PR creates documentation and folder guides; executable Terraform, Rego and workflow files remain pending.

```text
devsecops-policy-lab/
  README.md
  .gitignore
  .checkov.yaml
  .github/workflows/devsecops-gate.yml
  .github/CODEOWNERS
  configs/
    main.tf
    providers.tf
    versions.tf
    .terraform.lock.hcl
  examples/insecure/
    public-access/
    wrong-encryption/
    missing-tags/
  policy/
    s3_policy.rego
    s3_policy_test.rego
  tests/fixtures/
    README.md
    compliant.json
    violations.json
  scripts/
    check-policy.sh
    test-examples.sh
  docs/
    project.md
    implementation.md
    troubleshooting.md
    policy-catalog.md
    test-results.md
    weekly-log.md
    evidence/
```

Use configs as the normal scan target. Scan insecure examples in a separate test harness that expects specific policy failures. Do not treat any nonzero exit as a successful negative test: a missing executable or malformed configuration is not a detected security violation. Commit provider lock files for independently initialized Terraform roots. Ignore .terraform directories, state, raw plans, credentials and generated reports containing sensitive values. [10]

## Initial control catalogue

S3-01: all four bucket public-access-block flags must be explicitly true. S3-02: explicit SSE-KMS configuration is required by the lab standard. S3-03: versioning must be Enabled. S3-04: Owner and Environment tags must be nonempty; Environment must match an approved value. Catalogue each rule’s rationale, scope, Checkov overlap, expected findings and test cases.

S3 controls are represented by separate Terraform resources. Join them to the correct bucket; never infer protection from an unrelated bucket’s settings. Start with explicit known bucket names in the root module. Reject unknown or ambiguous associations. Module traversal and computed names are later extensions. Pure deletions must not be treated as creates; removal of a protection resource while a bucket remains must fail.

## 7 Work plan across twelve weeks

Weeks are relative to the university schedule because the official start date was not supplied. The project is approximately three weeks behind schedule. Weeks 1–3 below describe the foundation to complete now; they are not retrospective claims of completed work.

| Week | Work | Evidence or completion criterion |
| --- | --- | --- |
| 1 | Define scope and research | Project statement, source notes, threat model and exclusions. |
| 2 | Set up repository and tools | Recorded versions, directory structure and one valid S3 configuration. |
| 3 | Build first local checks | One Checkov pass/fail pair; one Rego rule with positive and negative tests. |
| 4 | Define policy coverage | Control catalogue and separate negative examples for each rule. |
| 5 | Generate real plans | Reproducible Terraform plan JSON; document provider and credential assumptions. |
| 6 | Complete OPA policies | All core rules; input validation, unknown values and resource association tests. |
| 7 | Automate PR checks | Validation, scans, policy tests and final aggregation in Actions. |
| 8 | Enforce merge gate | Required-check configuration; blocked PR and corrected PR evidence. |
| 9 | Test edge cases | Missing controls, deletions, replacements, malformed input and scanner errors. |
| 10 | Evaluate effectiveness | Expected/actual matrix, detection counts, false positives and run durations. |
| 11 | Improve reproducibility | Clean-clone rehearsal, dependency review and another person’s feedback if available. |
| 12 | Finalize and demonstrate | Final report, README, demo walkthrough, limitations and tagged release. |

## Time and scope management

Planning assumption: approximately 6–8 focused hours per normal week, with a 12–18 hour catch-up block for the first milestone. These are estimates, not recorded effort. If time is limited, complete three policies well before adding optional controls. Preserve weeks 9–12 for testing, analysis and presentation rather than adding new platforms.

## Final acceptance criteria

A compliant example passes all selected controls. Each deliberate violation produces the expected finding. Malformed input, undefined decisions and failed prerequisites block approval. A failing PR is visibly unmergeable under the configured rule. A fix produces a passing required check. Another person can reproduce the documented result from a clean checkout.

## 8 Current progress and catch up milestone

| Item | Evidence based status |
| --- | --- |
| Topic and initial design | Provided by the student: named topic, workflow sketch and repository proposal. |
| Architecture review | Completed in this report: corrected data flow, merge enforcement and test separation. |
| Research and schedule | Prepared in this report from official documentation; student review still needed. |
| Repository and installations | Repository verified on 19 October 2026: four original Markdown files on main. Local installations and tool versions remain unverified. |
| Code and local test results | Not verified. No Terraform, Rego or scanner logs supplied. |
| GitHub merge enforcement | Verified on 19 October 2026: zero workflow runs and main is not protected. Gate implementation and enforcement remain pending. |

## Current stage statement

The project is at the planning and architecture validation stage based on the evidence available. The initial scope and repository layout have been proposed, and this report provides the source analysis and implementation plan. The next milestone is a local proof of concept. Implementation and CI success must be reported only after execution evidence is collected.

## Practical catch up sequence

Session 1 — 2–3 hours: review the project plan, check out the repository, review the documentation and ignore rules, install tools and capture exact version outputs. Deliverable: repository skeleton and environment record.

Session 2 — 3–4 hours: create one compliant configuration and one isolated violation. Run Terraform validation and selected Checkov checks. Record findings and exit codes. Deliverable: one demonstrable scanner pass/fail pair.

Session 3 — 4–6 hours: write one small Rego rule and labelled synthetic JSON fixtures. Add positive, negative, missing-field and unknown-value tests. Verify that the shell wrapper returns failure for a denied or undefined decision. Deliverable: local policy proof of concept.

Session 4 — 3–5 hours: rerun from a clean checkout, record actual commands, dates and results, and prepare a short instructor demonstration. Attempt a basic PR workflow only if the local evidence is complete. Deliverable: a credible foundation milestone rather than an unsupported claim of a finished pipeline.

## Evidence required before claiming weeks 1 to 3 complete

Repository link and commit SHA; tool versions; source notes; valid Terraform example; expected and actual scanner output; Rego source and test output; exit-code evidence; dated weekly log. Do not backdate commits or state that tests passed without logs. The exact assessment equivalence of this catch-up milestone must be agreed with the instructor.

## 9 Problems and bugs

No runtime bugs have been observed because implementation evidence was not provided. The entries below are design issues identified during review and anticipated risks, not claims of executed fixes.

| Issue | Correction or planned verification |
| --- | --- |
| Plan described as HCL AST | Correct documentation: Terraform generates and exports the plan; OPA evaluates JSON. |
| Failed job assumed to block merge | Configure a required gate and demonstrate that merging is prevented. |
| Secure and insecure code mixed | Separate expected-failure fixtures from the normal scan target. |
| Unknown values silently accepted | Reject unknown security attributes and test missing or ambiguous associations. |
| OPA result gives misleading success | Require allow == true; test false, undefined, invalid input and evaluation errors. |
| Provider requires credentials | Use fixtures initially; validate a real plan path before claiming integration. |
| Encryption finding overstated | Describe explicit SSE-KMS policy separately from AWS default SSE-S3 encryption. |
| PR weakens its own gate | Review changes to policies, workflows, scripts and scanner config; document maintainer bypass limits. |

## Bug log format

For each observed issue record: ID, date, commit, environment, reproduction command, expected result, actual result, impact, root cause, attempted fix, verification evidence and status. Leave root cause as unknown until supported. Close an issue only after the original reproduction succeeds with the fix and a regression check exists where useful.

## Evaluation design

Prepare at least 12 controlled cases: compliant baseline; each of the four control violations; missing control resource; control attached to another bucket; unknown security value; invalid JSON; unsupported input structure; removal of protection while retaining the bucket; and a scanner or planning failure. Add a policy-edit regression test and a corrected PR demonstration.

For every case, record the expected result first. Then capture each tool’s result, the final gate result, finding IDs, exit code, commit and duration. Distinguish a correctly rejected policy violation from a blocked execution error. Report detection as detected intended violations divided by total intended violations; report compliant cases wrongly rejected separately. Small lab results must not be generalized to all AWS configurations.

## 10 Documentation and references

## Documentation maintenance

README.md provides scope, prerequisites, quick start, architecture and verified demo links. docs/project.md holds requirements, research and schedule. implementation.md records actual setup and commands. troubleshooting.md records observed defects. policy-catalog.md maps each rule to its rationale and tests. test-results.md contains expected and actual outcomes. weekly-log.md records dated work, evidence and next steps.

Store sanitized text logs and a small number of explanatory screenshots in docs/evidence. Name evidence with a date and test ID; include commit and tool versions in the test record. Do not commit raw state or plans: Terraform JSON can reveal sensitive values. Keep synthetic fixtures clearly marked, and retain only sanitized extracts of real output. [10]

## Bibliography

Official documentation reviewed 19 October 2026. These are technical sources rather than peer-reviewed journal articles. They support the design; the project’s experimental evidence must come from its own recorded runs.

[1] NIST. Secure Software Development Framework Version 1.1, SP 800-218 (2022).
https://csrc.nist.gov/pubs/sp/800/218/final

[2] Open Policy Agent. Terraform integration.
https://www.openpolicyagent.org/docs/terraform

[3] HashiCorp. Terraform JSON output format.
https://developer.hashicorp.com/terraform/internals/json-format

[4] Checkov. CLI command reference.
https://www.checkov.io/2.Basics/CLI%20Command%20Reference.html

[5] Open Policy Agent. CLI reference.
https://www.openpolicyagent.org/docs/cli

[6] Open Policy Agent. Policy language.
https://www.openpolicyagent.org/docs/policy-language

[7] GitHub. About protected branches.
https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches

[8] GitHub. Secure use reference.
https://docs.github.com/en/actions/reference/security/secure-use

[9] AWS. Default encryption FAQ for Amazon S3.
https://docs.aws.amazon.com/AmazonS3/latest/userguide/default-encryption-faq.html

[10] HashiCorp. Terraform show command reference.
https://developer.hashicorp.com/terraform/cli/commands/show
