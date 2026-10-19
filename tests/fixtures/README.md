# Policy fixtures

Add small synthetic JSON fixtures during implementation. Each fixture needs a documented expected decision and a label stating whether it is synthetic or derived from a sanitized real Terraform plan.

Never present synthetic input as evidence of successful Terraform planning. Keep fixtures small enough to explain each tested condition.
