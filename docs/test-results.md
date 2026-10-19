# Test matrix

All cases are **not run**. Expected behavior is the specification; fill actual results only after execution.

| ID | Scenario | Expected final outcome | Actual |
| --- | --- | --- | --- |
| T01 | Compliant baseline | Allow | Not run |
| T02 | Public access block disabled | Deny S3-01 | Not run |
| T03 | Encryption violates SSE-KMS requirement | Deny S3-02 | Not run |
| T04 | Versioning disabled | Deny S3-03 | Not run |
| T05 | Required tag missing | Deny S3-04 | Not run |
| T06 | Control resource missing | Deny applicable policy | Not run |
| T07 | Control belongs to another bucket | Deny applicable policy | Not run |
| T08 | Security-critical value unknown | Block | Not run |
| T09 | Invalid JSON | Block as execution/input error | Not run |
| T10 | Unsupported input structure | Block | Not run |
| T11 | Control deleted while bucket remains | Deny applicable policy | Not run |
| T12 | Scanner or Terraform fails to execute | Block as execution error | Not run |
| T13 | OPA decision undefined | Block | Not run |
| T14 | Mandatory upstream job skipped | Final gate fails | Not run |
| T15 | Policy weakened or rule removed | Regression test fails | Not run |
| T16 | Violating PR corrected | Required gate changes from fail to pass | Not run |

For each run append date, commit SHA, tool versions, exact command, expected findings, actual findings, exit code, duration, and evidence link. Separate policy violations from execution errors. An expected-failure test passes only when the intended violation is detected.

Report detected intended violations / total intended violations and compliant cases incorrectly blocked separately. Report measurements from actual runs, not estimates.
