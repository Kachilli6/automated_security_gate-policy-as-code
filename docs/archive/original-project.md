<!-- Historical proposal preserved from main at a33bef75e69f4fe46d28ab4cc1ad0651d950141e. Superseded by ../project.md; estimates and technical claims here are not validated. -->

# Project Plan, Research & Progress

## 1. Project Topic
Building an Automated Infrastructure-as-Code Security Gate using Terraform, Checkov, Open Policy Agent, and GitHub Actions.

## 2. Goal
Develop a working CI/CD pipeline that automatically evaluates Terraform code against static analysis benchmarks and custom Rego policy rules, blocking insecure deployments at the pull-request stage.

## 3. Tasks
- [ ] Write secure and intentionally insecure Terraform definitions (`main.tf`).
- [ ] Configure local static security scans using Checkov.
- [ ] Convert Terraform execution plans into JSON format (`tfplan.json`).
- [ ] Write custom policy-as-code rules in Rego (`s3_policy.rego`).
- [ ] Build a GitHub Actions workflow to run scans automatically on Pull Requests.
- [ ] Enforce branch protection rules to prevent non-compliant code merges.

## 4. Relevance
This project addresses real-world cloud security misconfigurations by embedding automated policy checks into developer workflows. It provides hands-on mastery of policy-as-code syntax, pipeline integration, and static analysis techniques.

## 5. Research & Analysis

### Official Documentation
* [HashiCorp Terraform Documentation](https://developer.hashicorp.com/terraform/docs)
* [Bridgecrew Checkov Documentation](https://www.checkov.io/1.Welcome/What%20is%20Checkov.html)
* [Open Policy Agent Documentation](https://www.openpolicyagent.org/docs/latest/)
* [GitHub Actions Documentation](https://docs.github.com/en/actions)

### Important Concepts
* **Shift-Left Security:** Moving security validations earlier in the development pipeline (e.g., at the PR stage rather than post-deployment).
* **Policy-as-Code (PaC):** Writing compliance, authorization, and governance rules as version-controlled code.
* **Declarative Rules (Rego):** Querying structured JSON execution plans to assert mandatory attributes or deny violations.

## 6. Methods and Tools
* **Software:** Git, Terraform CLI, OPA CLI, Checkov Python CLI.
* **Platform:** GitHub Actions (Ubuntu-latest runners).
* **Environment:** Local Linux / WSL2 workstation.
* **Methods:**
  * Iterative local testing using `terraform plan` and `opa eval`.
  * Live CI/CD evaluation using triggered GitHub Action runs on test branches.
 
## 7. Core Stack Inventory
* **Infrastructure as Code (IaC):** Terraform (HCL2 language syntax).
* **Off-the-Shelf Static Security Analysis:** Checkov (Static analysis scanner for infrastructure misconfigurations).
* **Custom Policy-as-Code Engine:** Open Policy Agent (OPA) with the Rego policy query language.
* **Automation & CI/CD Pipeline:** GitHub Actions (YAML workflow pipelines).
* **Version Control:** Git & GitHub.

## 8.

### System Resource Budget

| **Component** | **Minimum Requirement** | **Estimated Usage** | **Notes** |
|---|---|---|---|
| **Disk Space** | ~2.5 GB total | ~1.5 GB binaries & caches | Terraform (~150MB), OPA (~50MB), Checkov Python environment (~500MB), local `.git` cache and logs (~800MB). |
| **RAM** | 4 GB | ~500 MB active | Only consumed when running local static evaluations or CLI dry runs. |
| **CPU** | Any x86_64 / ARM64 dual-core | Minimal | Local runs take 2–5 seconds per evaluation. |
| **OS Support** | Linux / macOS / WSL2 | N/A | Fully native on Linux distributions (Ubuntu, RHEL, Debian). |

## 8. Work Plan

| Week | Tasks | Expected Result |
| :---: | :--- | :--- |
| **Week 1** | **Workspace & IaC Foundations**<br>• Initialize directory structure (`configs/`, `scripts/`, `policy/`).<br>• Install Terraform CLI and Python/pip locally.<br>• Author initial `main.tf` with baseline compliant and non-compliant AWS S3 bucket declarations. | Working local workspace with a functional `main.tf` ready for static analysis. |
| **Week 2** | **Off-the-Shelf Static Scanning**<br>• Install Checkov CLI.<br>• Execute local static scans against `main.tf`.<br>• Document identified misconfigurations and standard CIS benchmark rules in `implementation.md`. | CLI scan logs detailing detected security vulnerabilities and pass/fail states. |
| **Week 3** | **IaC Plan Generation & AST Parsing**<br>• Initialize Terraform working directory (`terraform init`).<br>• Generate binary execution plans (`terraform plan -out`).<br>• Convert plans to JSON format (`terraform show -json`). | A structured `tfplan.json` file representing the infrastructure state. |
| **Week 4** | **Rego Language & OPA Basics**<br>• Install Open Policy Agent (OPA) CLI locally.<br>• Complete core Styra Academy / Rego tutorials.<br>• Inspect `tfplan.json` data structure using `opa eval` queries. | Working local OPA environment and baseline Rego query comprehension. |
| **Week 5** | **Custom Policy Authoring (Part 1)**<br>• Draft `s3_policy.rego` to target public ACLs (`public-read`).<br>• Implement `deny` rules parsing resource change arrays.<br>• Test evaluation against `tfplan.json`. | Local Rego policy file successfully blocking public S3 bucket ACL configurations. |
| **Week 6** | **Custom Policy Authoring (Part 2)**<br>• Add mandatory tag compliance rules (e.g., required `Environment` tag).<br>• Refactor Rego logic to produce explicit error messages per violation.<br>• Document policy logic in `implementation.md`. | Robust `s3_policy.rego` enforcing both security ACLs and organizational metadata tags. |
| **Week 7** | **GitHub Actions Pipeline Setup**<br>• Create public GitHub repository and push project files.<br>• Draft `.github/workflows/devsecops-gate.yml`.<br>• Configure workflow triggers on `pull_request` events to `main`. | Automated GitHub Actions workflow triggering on incoming code changes. |
| **Week 8** | **Pipeline Tool Integration**<br>• Add Checkov Action step to pipeline.<br>• Add OPA setup and `opa eval` evaluation step to pipeline.<br>• Configure exit code assertions (`exit 1` on policy failure). | Complete CI/CD security gate executing full scans on every pull request. |
| **Week 9** | **Branch Protection & Gate Enforcement**<br>• Configure GitHub Branch Protection rules for `main`.<br>• Require status checks from the DevSecOps gate to pass before merging.<br>• Conduct positive test (compliant code merge). | Verified pipeline allowing compliant pull requests to merge cleanly. |
| **Week 10** | **Negative Testing & Edge Case Validation**<br>• Submit intentionally flawed PRs (public ACLs, missing tags).<br>• Verify that GitHub Actions blocks non-compliant merges.<br>• Log findings and edge-case behaviors in `troubleshooting.md`. | Confirmed security gate blocking non-compliant code with clear feedback. |
| **Week 11** | **Repository Documentation & Polish**<br>• Finalize all 4 Markdown files (`README.md`, `project.md`, `implementation.md`, `troubleshooting.md`).<br>• Add execution logs and pipeline run screenshots to `screenshots/`.<br>• Clean up workspace files and `configs/`. | Complete, reproducible open-source portfolio repository. |
| **Week 12** | **Academic Framing & finalizing project**<br>• Analyze pipeline execution latencies and developer friction metrics.<br>• Further research direction analysis (Policy-as-Code vs. Software Supply Chain). | A completed functional DevSecOps project. |
