# Policy catalogue

Status: proposed requirements; no policy implementations or Checkov mappings are verified yet.

| ID | Lab requirement | Expected violation |
| --- | --- | --- |
| S3-01 | All four public-access-block flags explicitly true for each bucket | Missing block resource, false/unknown flag, or ambiguous bucket association |
| S3-02 | Explicit default encryption algorithm aws:kms | Missing explicit configuration, wrong/unknown algorithm |
| S3-03 | Bucket versioning Enabled | Missing, suspended, or unknown versioning |
| S3-04 | Nonempty Owner and Environment; Environment in dev, test, prod | Missing/blank tags or unapproved environment |

SSE-KMS is a lab requirement beyond AWS default SSE-S3 for new uploads. Versioning and tags support recovery/governance; they are not proofs of access security.

## Input and decision contract

- Initial scope: known literal bucket names in a root module.
- Associate separate S3 control resources with the correct bucket.
- Reject invalid input and unsupported structures; never count a missing decision as approval.
- Unknown security values and ambiguous associations block evaluation.
- Distinguish a deleted bucket from a retained bucket losing its controls.
- A compliant zero-change plan is not automatically an error; define and test its handling.
- Define behavior for an empty plan explicitly and prevent unsupported resources from silently broadening coverage.

## Implementation record for each rule

Record Rego package/query, Checkov IDs and versions, rationale, positive test, negative test, edge cases, finding text, and limitations. Populate exact Checkov IDs only after validating them against the installed version.

Policy changes and scanner suppressions require a written rationale and regression evidence.
