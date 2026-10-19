# Planned security workflow

Add devsecops-gate.yml after local checks are reproducible. This Markdown guide is not an executable workflow.

Required design: PR trigger, minimal permissions, pinned dependencies, Terraform validation, Checkov, real plan JSON evaluation, policy tests, and an always-run final job that requires every mandatory dependency to succeed. No apply or cloud secrets in PR jobs. Verify merge enforcement separately in repository settings.
