# First milestone — weeks 1 to 3

Goal: a credible local proof of concept with dated evidence. This checklist describes work to complete now, not work completed in past weeks.

## Foundation

- [ ] Review and understand the revised project plan.
- [ ] Merge the organization PR after review.
- [ ] Clone the repository and create a feature branch.
- [ ] Install tools and record actual versions in implementation.md.
- [ ] Confirm the S3 policy scope and threat model.

## First experiments

- [ ] Add one valid root-module S3 Terraform configuration.
- [ ] Run formatting and validation.
- [ ] Record selected Checkov IDs and their meanings.
- [ ] Capture one compliant scan and one deliberately violating scan.
- [ ] Write one Rego v1 policy with a clear decision contract.
- [ ] Add labelled synthetic compliant, violating, missing-field, and unknown-value inputs.
- [ ] Test explicit allow, deny, undefined decision, and execution-error exit behavior.

## Evidence and instructor demonstration

- [ ] Record commands, versions, commit SHA, and exit codes.
- [ ] Store sanitized evidence and fill the test matrix.
- [ ] Reproduce from a clean checkout.
- [ ] Update the weekly log using actual dates.
- [ ] Demonstrate the local checks and explain what CI integration still needs.

Estimated effort: 12–18 focused hours, depending on installation and learning time. A real Terraform plan and GitHub merge enforcement remain separate milestones. Confirm the university's exact assessment expectations with the instructor.
